# Reactive Programming with RxJava

by Tomasz Nurkiewicz & Ben Christensen.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](https://www.amazon.com/Reactive-Programming-RxJava-Asynchronous-Applications/dp/1491931655)!_

## Chapter 1: Reactive Programming with RxJava

### When You Need Reactive Programming

- Responding to latency-bound IO events.
- Handle generic push-based events/data.

If the code in question is handling _only one event stream_, reactive-imperative programming with a callback is going to be fine, and bringing in reactive-functional programming is not going to give you much benefit. You can have hundreds of different event streams, and if they are all _completely independent of one another_, imperative programming is not likely to be a problem. In such straightforward use cases, imperative approaches are going to be the most efficient because they eliminate the abstraction layer of reactive programming and stay closer to that for which current operating systems, languages, and compilers are optimized.

If your program is like most though, you need to combine events, have conditional logic interacting between them, and must handle failure scenarios and resource cleanup on any and all of them. This is where the reactive-imperative approach begins to dramatically increase in complexity and reactive-functional programming begins to shine.

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
