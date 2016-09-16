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

  @Injectpublic TwitterApplication(Tweeter tweeter, Timeline timeline) {
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
