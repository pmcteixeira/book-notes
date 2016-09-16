# Dependency Injection with Dagger 2

by Jake Wharton at Devoxx 2014.

[Slides](https://speakerdeck.com/jakewharton/dependency-injection-with-dagger-2-devoxx-2014) [Video](https://www.youtube.com/watch?v=plK0zyRLIP8)

## 1\. Issue about Manual DI

```java
// Library code
public class TwitterApi {
  private final OkHttpClient client;

  public TwitterApi(OkHttpClient client) {
    this.client = client;
  }

  public void postTweet(String user, String tweet) {
    Request request = // TODO build POST request ...
    client.newCall(request).execute();
  }
}

public class Tweeter {
  private final TwitterApi api;
  private final String user;

  public Tweeter(TwitterApi api, String user) {
    this.api = api;
    this.user = user;
  }

  public void tweet(String tweet) {
    api.postTweet(user, tweet);
  }
}

public class Timeline {
  private final List<Tweet> cache = new ArrayList<>();
  private final TwitterApi api;
  private final String user;

  public Timeline(TwitterApi api, String user) {
    this.api = api;
    this.user = user;
  }

  public List<Tweet> get() {/* ... */}
  public void loadMore(int amount) {/* ... */}
}
```

```java
// Calling code
OkHttpClient client = new OkHttpClient();
TwitterApi api = new TwitterApi(client);
String user = "Jake Wharton";

Tweeter = new new Tweeter(api, user);
tweeter.tweet("Hello, #Devoxx 2014!");

Timeline timeline = new Timeline(api, user);
timeline.loadMore(20);
for (Tweet tweet : timeline.get()) {
  System.out.println(tweet);
}
```

By having `Tweeter` and `Timeline` taking the same `TwitterApi` instance, they reuse resources and neither are responsible for creating or configuring. However, while this ends up being a lot nicer for the library code, we've now polluted the calling code, as it is now responsible for manually creating all the dependencies and performing the injection. If all I want to do is tweet and read my timeline, I don't want to be burdened by having to write this boilerplate code.

## 2\. Dagger API

### Overview

- `@Module` + `@Provides`: mechanism for providing dependencies.
- `@Inject`: mechanism for requesting dependencies.
- `@Component`: bridge between modules and injections.

### Providing Dependencies

- Modules are classes whose methods provide dependencies.
- `@Module` on the class.
- `@Provides` (and friends) on each method.

We know we need `TwitterApi` and `OkHttpClient` instances. So, I written up a simple module class (annotated with `@Module`) which has methods (annotated with `@Provides`) that instantiate these. Because these objects are relatively expensive and reusable, we only want to create one instance of them. Rather than having to manage that ourselves, we allow Dagger to do so through using also the `@Singleton` annotation.

```java
@Module
public class NetworkModule {

  @Provides @Singleton
  OkHttpClient provideOkHttpClient() {
    return new OkHttpClient();
  }

  @Provides @Singleton
  TwitterApi provideTwitterApi(OkHttpClient client) {
    return new TwitterApi(client);
  }
}
```

When Dagger is parsing the module, internally it's creating a graph of how these dependencies relate to each other. The module has two methods and Dagger is going to look at the return type of those methods and associate them (type -> method). The `provideTwitterApi` method takes a `OkHttpClient` object as a parameter, and Dagger also creates an association (method -> type).

Modules are designed to be partitions and then composed back together to perform a complete graph. Having a single module listing hundreds of dependencies is _not_ intended. Modules should be structured the way it makes sense semantically, given how the application and libraries are structured.

Next we need a `Tweeter` and `Timeline` instances, thus we'll create a `TwitterModule`. This one is a bit unique, because both the tweeter and timeline required the username in order to be instantiated. The `NetworkModule` had an _implicit_ constructor, but `TwitterModule` will have an _explicit_ one, and take in the username and add it to the graph. This is the way which you inject state into the graph. Now we can provide methods for both the tweeter and the timeline.

```java
@Module
public class TwitterModule {
  private final String user;

  public TwitterModule(String user) {
    this.user = user;
  }

  @Provides @Singleton
  Tweeter provideTweeter(TwitterApi api) {
    return new Tweeter(api, user);
  }

  @Provides @Singleton
  Timeline provideTimeline(TwitterApi api) {
    return new Timeline(api, user);
  }
}
```

You'll notice that both of them take the `TwitterApi` -- _which is provided in the separate module_ -- and both are using the user field which comes in through the constructor. Once the `TwitterModule` is added to the graph, Dagger will create an association between the provider methods and the `TwitterApi` type found in `NetworkModule`. This way, when Dagger uses for example the `provideTweeter` method, it follows the transitive dependencies, and resolve them first. It will find a `TwitterApi` first and then follow in turn its transitive dependencies, ultimately until it gets to the very leaf nodes of the graph which no longer require dependencies to instantiate.

Given that `TwitterApi` is annotated with `@Singleton`, once Dagger uses `provideTimeline` method it will reuse the retained instance which was created for `provideTweeter`.

### Requesting Dependencies

- `@Inject` on a single constructor.
- Constructor, field, and method injection.

#### Constructor injection.

- `@Inject` on a single constructor.
- Constructor parameters are dependencies.
- Dependencies can be stored in private and final fields.
- Implicitly made available for downstream injection.

You can only have a single constructor in a class which has the `@Inject` annotation, and the parameters ultimately become the dependencies which are going to be requested from Dagger. _One big advantage of this is that you can make immutable objects by storing these in private final fields_.

If I have a application which encapsulates `Tweeter` and `Timeline`, I can take these as constructor arguments with an `@Inject` annotation on top of it.

```java
public class TwitterApplication {
  private final Tweeter tweeter;
  private final Timeline timeline;

  @Inject
  public TwitterApplication(Tweeter tweeter, Timeline timeline) {
    this.tweeter = tweeter;
    this.timeline = timeline;
  }

  // ...
}
```

By having `@Inject` annotated in constructor, Dagger knows how to create instances of that object and it's implicitly made available for downstream injection. What this means is that if I have another class that wants to inject that `TwitterApplication` it can do so.

For that reason, we can eliminate for example the `TwitterApi` provides method from `NetworkModule` if we add an `@Inject` annotation to its constructor. In the module, the `TwitterApi` was a singleton, and solely when you have a `@Inject` constructor, Dagger is going to invoke the constructor every time somebody request the `TwitterApi`, ultimately ending with multiple instances of this class. In order to maintain it as a Singleton, `TwitterApi` should be annotated with the `@Singleton` annotation in the class declaration:

```java
@Singleton
public class TwitterApi {
  private final OkHttpClient client;

  @Inject
  public TwitterApi(OkHttpClient client) {
    this.client = client;
  }

  public void postTweet(String user, String tweet) {
    Request request = // TODO build POST request ...
    client.newCall(request).execute();
  }
}
```

#### Method injection

- `@Inject` on methods

Method injection allows methods to be invoked with dependencies. You place `@Inject` on any number of methods. Similar to constructor invocation, the parameters become the dependencies. And, this happens **after the object constructor** has completed.

There's really only one good case for this, and that's when you need to pass an instance of yourself to a dependency. You don't want to do this inside the constructor, so we do this inside the method injection, like so:

```java
public class TwitterApplication {

  //...

  @Injectpublic void enableStreaming(Streaming streaming) {
    streaming.register(this);
  }
}
```

The `Streaming` wrapper is injected then `TwitterApplication` instance is registered as a listener.

#### Field Injection

- `@Inject` on fields for dependencies
- Field may not be private of final.
- Injection happens after the object is fully instantiated.

This is very useful for Android where the Activity object is created by the system. Field injection happens after constructor injection but before method injection.

An interesting thing about field and method injection is that the object usually has to be aware that field injection is happening, simply because that after the constructor has completed there still might be unresolved dependencies. Same for method injection.

```java
public class TwitterApplication {
  @Inject Tweeter tweeter;
  @Inject Timeline timeline;

  // ...
}
```

### Components

- Bridge between modules and injection.
- The injector.
- Implementation of scopes.

We provide objects with modules and request injection with `@Inject`, the mechanism to bridge the two together is called _components_. The Component is just an interface and it has the `@Component` annotation on top of it. In our annotation we list the modules which make up that component so both `NetworkModule` and `TwitterModule` are contributing.

Components are aware of the Scope of dependencies. Since all modules in this component are singletons, we can also annotate the component with `@Singleton`.

The way to expose types out of the component is simply by writing abstract method declarations.

```java
@Singleton
@Component(modules = {
  NetworkModule.class,
  TwitterModule.class,
})
public interface TwitterComponent {
  Tweeter tweeter();
  Timeline timeline();
}
```

Dagger generates an implementation of this interface.

```java
TwitterComponent component = Dagger_TwitterComponent.builder()
  .networkModule(new NetworkModule())
  .twitterModule(new TwitterModule("Jake Wharton"))
  .build();

Tweeter tweeter = component.tweeter();
tweeter.tweet("Hello, #Devoxx 2014!");

Timeline timeline = component.timeline();
timeline.loadMore(20);
for (Tweet tweet : timeline.get()) {
  System.out.println(tweet);
}
```

The `NetworkModule` has a implicit default constructor. So we can just let Dagger create that module ourselves and we only have to pass in modules that require external state.

The TwitterApplication has `@Inject` constructor and it is implicitly available to be injected downstream. That also means it's available to be exposed by a component.

_When we only have field injection and/or method injection, we can actually just put a method on the component which accepts an instance of that type and it will perform field or method injection on it._ We construct the component as we did before but now we create our application ourselves, passes to Dagger, Dagger is going to set the fields on that object and then ultimately we can run our application.

```java
@Singleton
@Component(modules = {
  NetworkModule.class,
  TwitterModule.class
})
interface TwitterComponent {
  void injectActivity(TwitterActivity activity);
}
```

```java
TwitterComponent component = Dagger_TwitterComponent.builder().
  twitterModule(new TwitterModule("JakeWharton"))
  .build();

TwitterActivity activity = // Android creates instance...
component.injectActivity(activity);
```

There's actually a very cool property that you can turn this into a method that actually return the same instance. That allows to have a kind of builder pattern when you invoke the component and immediately get back the injected type.

```java
@Singleton
@Component(modules = {
  NetworkModule.class,
  TwitterModule.class
})
interface TwitterComponent {
  TwitterActivity injectActivity(TwitterActivity activity);
}
```

#### Implementation of scopes

Components are the way we get concrete scopes in Dagger 2\. To illustrate this we'll break up our component. We have two modules, and each module have different responsibilities.

```java
@Singleton
@Component(modules = NetworkModule.class)
public interface ApiComponent {

}

@Component(modules = TwitterModule.class)
public interface TwitterComponent {
  TwitterApplication app();
}
```

What we're going to do is to add another field onto the component which explicitly says a dependency.

```java
@Singleton
@Component(
  dependencies = ApiComponent.class,
  modules = TwitterModule.class
)
public interface TwitterComponent {
  TwitterApplication app();
}
```

So we have a dependency on the `ApiComponent`. The `TwitterComponent` can no longer be created unless we have an instance of the `ApiComponent`. Now an interesting property of how component work is that this will actually end up failing if I tried to build it. And that's because in `TwitterModule` both the `Tweeter` and the `Timeline` require an instance of `TwitterApi` class. **Components do not expose any types from their modules unless you explicitly make them available**. So that means I have to add a method saying that the `TwitterApi` instance is going to be exposed from the `ApiComponent` and now the `TwitterComponent` can use it to create the TwitterApplication.

```java
@Component(modules = NetworkModule.class)
public interface ApiComponent {
  TwitterApi api();
}
```

If we added `@Singleton` annotation to `TwitterComponent` the build would also fail. Currently, scopes are limited to a single component. And because we cannot have the `@Singleton` annotation in that component, we also cannot have it on the modules which that component uses. Components without scope annotations are called _unscoped_ components. This will also make the module instances to be created more than once if called multiple times.

So now that components are broken apart, we create the `TwitterComponent` by adding reference to `ApiComponent` in its builder.

```java
// create() is used when every module can be implicitly created
ApiComponent apiComponent = Dagger_ApiComponent.create();

TwitterComponent twitterComponent = Dagger_TwitterComponent.builder()
  .apiComponent(apiComponent)
  .twitterModule(new TwitterModule("Jake Wharton"))
  .build();

twitterComponent.app().run();
```

### Scope Annotations
