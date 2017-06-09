# Reactive Programming with RxJava

by Tomasz Nurkiewicz & Ben Christensen.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](https://www.amazon.com/Reactive-Programming-RxJava-Asynchronous-Applications/dp/1491931655)!_

## Chapter 1: Reactive Programming with RxJava

### When You Need Reactive Programming

- Responding to latency-bound IO events.
- Handle external push-based events/data.

> If the code in question is handling _only one event stream_, reactive-imperative programming with a callback is going to be fine, and bringing in reactive-functional programming is not going to give you much benefit. You can have hundreds of different event streams, and if they are all _completely independent of one another_, imperative programming is not likely to be a problem. In such straightforward use cases, imperative approaches are going to be the most efficient because they eliminate the abstraction layer of reactive programming and stay closer to that for which current operating systems, languages, and compilers are optimized. If your program is like most though, you need to combine events, have conditional logic interacting between them, and must handle failure scenarios and resource cleanup on any and all of them. This is where the reactive-imperative approach begins to dramatically increase in complexity and reactive-functional programming begins to shine.

### How RxJava Works

Central to RxJava is the `Observable` type that represents a stream of data or events. It is intended for push (reactive) but can also be used for pull (interactive). It is lazy rather than eager. It can be used asynchronously or synchronously. It can represent 0, 1, many, or infinite values or events over time.

`Observable` supports events being pushed at it, but also supports an asynchronous feedback channel (async-pull/reactive-pull) as an approach to control backpressure. `Observable`/`Observer` pair connect via subscription. The `Observable` represents the stream of data and can be subscribed to by an `Observer`. Upon subscription, the `Observer` can have three types of events pushed to it:

```java
interface Observer<T> {
  void onNext(T t)
  void onError(Throwable t)
  void onCompleted()
}
```

The `onNext()` method might never be called or might be called once, many, or infinite times. The `onError()` and `onCompleted()` are terminal events, meaning that only one of them can be called and only once. When a terminal event is called, the `Observable` stream is finished and no further events can be sent over it. Terminal events might never occur if the stream is infinite and does not fail. There is an additional type of signature to permit interactive-pull:

```java
interface Producer {
  void request(long n)
}
```

This is used with a more advanced `Observer` called `Subscriber`:

```java
interface Subscriber<T> implements Observer<T>, Subscription {
  void onNext(T t)
  void onError(Throwable t)
  void onCompleted()
  ...
  void unsubscribe()
  void setProducer(Producer p)
}
```

The `unsubcribe` function as part of the `Subscription` interface is used to allow a subscriber to unsubscribe from an `Observable` stream. The `setProducer` function and `Producer` types are used to form a bidirectional communication channel between the producer and consumer used for flow control.

> Generally, an `Observable` is going to be asynchronous, but it doesn't need to be. An `Observable` can be synchronous, and in fact defaults to being synchronous. RxJava never adds concurrency unless it is asked to do so. A synchronous `Observable` would be subscribed to, emit all data using the subscriber's thread, and complete (if finite). An `Observable` backed by blocking network I/O would synchronously block the subscribing thread and then emit via onNext() when the blocking network I/O returned.

### Operators

RxJava uses the large API of operators used to manipulate, combine, and transform data. Meaning that they perform their computation inside the `onNext()` as the events pass by. Most of these operators are synchronous (unless a specific operator needs to be async, such as `timeout` or `observeOn`).

### Concurrency

Individual `Observable` streams permit neither concurrency nor parallelism. Instead, they are achieved via **composition** of _async_ `Observables` [e.g: using `merge` operator]. The contract of an RxJava `Observable` is that events (`onNext()`, `onCompleted()`, `onError()`) can never be emitted concurrently. In other words, a single `Observable` stream must always be serialized and thread-safe. Each event can be emitted from a different thread, as long as the emissions are not concurrent. This means no interleaving or simultaneous execution of `onNext()`. If `onNext()` is still being executed on one thread, another thread cannot begin invoking it again (interleaving).

### Lazy versus Eager

The `Observable` type is _lazy_, meaning it does nothing until it is subscribed to, so that all composition can be done before data starts flowing. Subscription, not construction starts work. Construction define what work should be done when it is eventually subscribed to. Observables can be reused because the `Observable` is lazy, it also means a particular instance can be invoked more than once.

### Duality

An Rx `Observable` is the async "dual" of an `Iterable`.

Pull (Iterable)  | Push (Observable)
---------------- | :----------------:
T next()         |     onNext(T)
throws Exception | onError(Throwable)
returns          |   onCompleted()

### Zero to Infinity

Zero                        |          One          | Many
--------------------------- | :-------------------: | -------------------------
`Completable doSomething()` | `Single<T> getData()` | `Observable<T> getData()`

**Completable:**

- It can respond with an error
- Never respond
- Respond with completion

**Single:**

- It can respond with an error
- Never respond
- Respond with a success

**Observable:**

- It can respond with an error
- Never respond
- Respond successfully with no data and terminate
- Respond successfully with a single value and terminate
- Respond successfully with multiple values and terminate
- Respond successfully with one or more values and never terminate (waiting for more data)

## Chapter 2: Reactive Extensions

### Subscribing to Notifications from Observable

To begin watching an `Observable`, you use the `subscribe()` family of methods:

```java
Observable<Tweet> tweets = //...

// onNext()
tweets.subscribe((Tweet tweet) ->
  System.out.println(tweet));

// onNext() + onError()
tweets.subscribe(
  (Tweet tweet) -> { System.out.println(tweet); },
  (Throwable t) -> { t.printStackTrace(); }
);

// onNext() + onError() + onCompleted()
tweets.subscribe(
  (Tweet tweet) -> { System.out.println(tweet); },
  (Throwable t) -> { t.printStackTrace(); },
  () -> {this.noMore();}
);
```

As a side note, often you can use Java 8 method references instead of _lambdas_ to improve readability, as illustrated here:

```java
tweets.subscribe(
  System.out::println,
  Throwable::printStackTrace,
  this::noMore);
```

### Capturing All Notifications by Using `Observer<T>`

`Observer<T>` wraps all three:

```java
Observer<Tweet> observer = new Observer<Tweet>() {
  @Override
  public void onNext(Tweet tweet) {
    System.out.println(tweet);
  }

  @Override
  public void onError(Throwable e) {
    e.printStackTrace();
  }

  @Override
  public void onCompleted() {
    noMore();
  }
};

tweets.subscribe(observer);
```

### Controlling _listeners_ using `Subscription` and `Subscriber<T>`

```java
Subscription subscription =
tweets.subscribe(System.out::println);
//...
subscription.unsubscribe();
```

```java
Subscriber<Tweet> subscriber = new Subscriber<Tweet>() {
  @Override
  public void onNext(Tweet tweet) {
    if (tweet.getText().contains("Java")) {
      unsubscribe();
    }
  }

  @Override
  public void onCompleted() {}

  @Override
  public void onError(Throwable e) {
    e.printStackTrace();
  }
};

tweets.subscribe(subscriber);
```

### Creating Observables

- `Observable.just(value)` emits exactly one value.

- `Observable.from(values)` emits `Iterable<T>` or `T[]`.

- `Observable.range(from, n)` emits _n_ integer numbers starting from _from_.

- `Observable.empty()` emits only an `onCompleted()`.

- `Observable.never()` never emits. Useful for testing.

- `Observable.error()` emits only an `onError()`.

### Custom Observables

```java
Observable<Integer> ints = Observable
  .create(new Observable.OnSubscribe<Integer>() {
    @Override
    public void call(Subscriber<? super Integer> subscriber) {
      try {
        subscriber.onNext(1);
        subscriber.onNext(2);
        subscriber.onNext(3);
        subscriber.onCompleted();
      } catch (Exception e) {
        subscriber.onError(e);
      }
    }
  });
```

The pattern of completing an `Observable` with **one value** and wrapping with the `try-catch` statement is so prevalent that the built-in `fromCallable()` operator was introduced:

```java
Observable<Data> rxLoad(int id) {
  return Observable.fromCallable(() ->
    load(id));
}

// is equivalent to:
```

### Managing multiple subscribers

`Observable.just(x)` emits _x_ to every subscriber by design. However, it is beneficial to share heavy operations (e.g. db queries) among subscribers. There exists a handy `cache()` operator:

```java
Observable<Integer> ints = Observable.<Integer>create(subscriber -> {
  // ...
})
.cache();
```

Operators _wrap_ existing Observables, typically intercepting the subscription. `cache()` stands between `subscribe()` and `Observable`. On first subscriber, `cache()` delegates subscription to the underlying `Observable` and forwards all events downstream. At the same time, it keeps a copy of all notifications internally. For subsequent subscribers, delegation is no longer made to `Observable` and instead cached values are emitted.

### Infinite streams

It is advised to check the `isUnsubscribed()` flag as often as possible to avoid sending events after a subscriber no longer wants to receive new events.

```java
//...
BigInteger i = ZERO;
while (!subscriber.isUnsubscribed()) {
  subscriber.onNext(i);
  i = i.add(ONE);
}
//...
```

Handling unsubscription immediately before trying to send an event is fine as long as events are pushed relatively often. `Observable` can only determine that a subscriber unsubscribed when it attempts to push some event to it. For long operations per event, the `Observable` can be notified on unsubscription, stopping the operation and cleaning resources, not having to wait for the next event.

```java
static <T> Observable<T> slowObservable(T x) {
  return Observable.create(
    subscriber -> {
      // ...
      subscriber.add(Subscriptions.create(/* interruption instruction */));
  });
}
```

`Subscriber.add()` registers a callback used to signal unsubscription.

### Timing: timer() and interval()

> Note: both operators **use threads underneath**.

```java
// Creates an Observable that emits long value of zero after specified delay time.
Observable
  .timer(1, TimeUnit.SECONDS)
  .subscribe((Long zero) -> log(zero));
```

```java
// Unlike range(), places a fixed delay before every event, including the first one
Observable
  .interval(1_000_000 / 60, MICROSECONDS)
  .subscribe((Long i) -> log(i));
```

### Hot and Cold Observables

- A **cold** `Observable` is entirely lazy and never begins to emit events until someone is actually interested. Thus, dependent on the `Subscriber`. Subscribing to a cold `Observable` often involves a side effect happening inside `create()`. For example, db is queried or a connection is opened.

- A **hot** `Observable` might already be emitting events no matter how many Subscribers they have (e.g: button clicks, etc). The presence or absence of Subscriber does not alter the behavior of Observable; it is entirely decoupled and independent.

No matter when you subscribe to a cold `Observable` -- immediately or after hours -- you always receive a complete and consistent set of events. On the other hand, if the Observable is hot, you can never be sure you received all events from the beginning. Technically, the events can be buffered, allowing subsequent subscribers to receive the same sequence of events. However, because it consumes theoretically an unlimited amount of memory, be careful with caching hot `Observables`.

> Surprisingly, `Observable.interval()` is not hot. You might think it simply produces timer ticks with some interval, irrespective of the environment, but in reality the timer events are produced only when someone subscribes and each subscriber receives independent stream. This is a definition of a cold `Observable`.

### Manually managing subscribers

#### Subject

The `Subject` extends `Observable` and _implements_ `Observer` at the same time. What that means is that you can treat it as `Observable` on the client side (subscribing to upstream events) and as `Observer` on the provider side (pushing events downstream on demand by calling `onNext()` on it). Typically, what you do is keep a reference to `Subject` internally so that you can push events from any source you like but externally expose this `Subject` as `Observable`.

```java
class TwitterSubject {
  private final PublishSubject<Status> subject = PublishSubject.create();

  public TwitterSubject() {
    TwitterStream twitterStream = new TwitterStreamFactory().getInstance();
    twitterStream.addListener(new StatusListener() {
      @Override
      public void onStatus(Status status) {
        subject.onNext(status);
      }
      @Override
      public void onException(Exception ex) {
        subject.onError(ex);
      }
      //other callbacks
    });

    twitterStream.sample();
  }

  public Observable<Status> observe() {
    return subject;
  }
}
```

`PublishSubject` is one of the flavors (subclasses) of `Subject`. We **eagerly** begin receiving events from the upstream system and simply push them (by calling `subject.onNext(...)`) **to all Subscribers**. Subject keeps track of them internally.

Other types of `Subject` are:

- `AsyncSubject` Remembers last emitted value and pushes it to subscribers when `onComplete()` is called. As long as AsyncSubject has not completed, events except the last one are discarded.

- `BehaviorSubject` Pushes all events emitted after subscription happened, just like `PublishSubject`. However, first it emits the most recent event that occurred just before subscription. This allows `Subscriber` to be immediately notified about the state of the stream. For example, `Subject` may represent the current temperature broadcasted every minute. When a client subscribes, he will receive the last seen temperature immediately rather than waiting several seconds for the next event. But the same `Subscriber` is not interested in historical temperatures, only the last one. If no events have yet been emitted, a special default event is pushed first (if provided).

- `ReplaySubject` The most interesting type of `Subject` that caches events pushed through the entire history. If someone subscribes, first he receives a batch of missed (cached) events and only later events in real-time. By default, all events since the creation of this `Subject` are cached. This can be become dangerous if the stream is infinite or very long. In that case, there are overloaded versions of `ReplaySubject` that keep only the following:

  - Configurable number of events in memory (`createWithSize()``)

  - Configurable time window of most recent events (`createWithTime()``)

  - Or even constraint both size and time (whichever limit is reached first) with `createWithTimeAndSize()`

> `Subjects` should be treated with caution: often there are more idiomatic ways of sharing subscriptions and caching events -- for example, see `ConnectableObservable`.

#### ConnectableObservable

`ConnectableObservable` is a type of Observable that ensures there exists at most one `Subscriber` at all times, but in reality there can be many of them sharing the same underlying resource.

There are many applications of `ConnectableObservable`; for example, making sure all `Subscribers` receive the same sequence of events regardless of when they subscribed. `ConnectableObservable` can also force subscription if it generates important side effects, even when no "real" `Subscriber` has appeared yet.

Subjects are imperative ways of creating Observables, whereas `ConnectableObservable` shields the original upstream `Observable` and guarantees at most one `Subscriber` reaches it. No matter how many Subscribers connect to `ConnectableObservable`, it opens just one subscription to the `Observable` from which it was created.

#### Single Subscription with `publish().refCount()`
