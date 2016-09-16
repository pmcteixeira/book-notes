# Dependency Injection with Dagger 2

by Jake Wharton at Devoxx 2014.

[Slides](https://speakerdeck.com/jakewharton/dependency-injection-with-dagger-2-devoxx-2014) [Video](https://www.youtube.com/watch?v=plK0zyRLIP8)

## 1\. Manual DI

```java
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
Tweeter = new new Tweeter("JakeWharton");
tweeter.tweet("Hello, #Devoxx 2014!");

Timeline timeline = new Timeline("JakeWharton");
timeline.loadMore(20);
for (Tweet tweet : timeline.get()) {
  System.out.println(tweet);
}
```
