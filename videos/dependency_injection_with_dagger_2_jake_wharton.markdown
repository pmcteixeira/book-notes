# Dependency Injection with Dagger 2

by Jake Wharton at Devoxx 2014.

[Slides](https://speakerdeck.com/jakewharton/dependency-injection-with-dagger-2-devoxx-2014) [Video](https://www.youtube.com/watch?v=plK0zyRLIP8)

## 1\. Manual DI

```java
public class TwitterApi {
  public void postTweet(String user, String tweet) {
    OkHttpClient client = new OkHttpClient();
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

By having `Tweeter` and `Timeline` taking the same `TwitterApi` instance, they reuse resources and neither are responsible for creating or configuring. However, while this ends up being a lot nicer for the library code, we've now polluted the calling code, as it is now responsible for manually creating all the dependencies and performing the injection.

If all I want to do is tweet and read my timeline, I don't want to be burdened by having to write this boilerplate code.
