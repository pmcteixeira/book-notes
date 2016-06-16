# Java Concurrency in Practice

by Brian Goetz with Tim Peierls, Joshua Bloch, Joseph Bowbeer, David Holmes, and Doug Lea.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601)!_

## Chapter 1: Introduction

### A (very) brief history of Concurrency

In the ancient past, computers didn't have operating systems; they executed a single program from beginning to end, and that program had direct access to all the resources of the machine.

Operating systems evolved to allow more than one program to run at once, running individual programs in **_processes_**: isolated, independently executing programs to which the operating system allocates resources such as memory, file handles, and security credentials. If they needed to, processes could communicate with one another through a variety of coarse-grained communication mechanisms: sockets, signal handlers, shared memory, semaphores, and files.

The same concerns (resource utilization, fairness, and convenience) that motivated the development of processes also motivated the development of threads. Threads allow multiple streams of program control flow to coexist within a process. **_They share process-wide resources such as memory and file handles, but each thread has its own program counter, stack, and local variables_**. Threads also provide a natural decomposition for exploiting hardware parallelism on multiprocessor systems; multiple threads within the same program can be scheduled simultaneously on multiple CPUs.

Threads are sometimes called lightweight processes, and most modern operating systems treat threads, not processes, as the basic units of scheduling. In the absence of explicit coordination, threads execute simultaneously and asynchronously with respect to one another. Since threads share the memory address space of their owning process, all threads within a process have access to the same variables and allocate objects from the same heap, which allows finer-grained data sharing than inter-process mechanisms. **_But without explicit synchronization to coordinate access to shared data, a thread may modify variables that another thread is in the middle of using, with unpredictable results_**.

### Exploiting multiple processors

Since the basic unit of scheduling is the thread, a program with only one thread can run on at most one processor at a time. On a two-processor system, a single-threaded program is giving up access to half the available CPU resources; On the other hand, programs with multiple active threads can execute simultaneously on multiple processors.

Using multiple threads can also help achieve better throughput on single processor systems. If a program is single-threaded, the processor remains idle while it waits for a synchronous I/O operation to complete. In a multithreaded program, another thread can still run while the first thread is waiting for the I/O to complete, allowing the application to still make progress during the blocking I/O.

### More responsive user interfaces

GUI applications used to be single-threaded, which meant that you had to either frequently poll throughout the code for input events (which is messy and intrusive) or execute all application code indirectly through a "main event loop". If code called from the main event loop takes too long to execute, the user interface appears to "freeze" until that code finishes, because subsequent user interface events cannot be processed until control is returned to the main event loop. Modern GUI frameworks, such as the AWT and Swing toolkits, replace the main event loop with an event dispatch thread (EDT). When a user interface event such as a button press occurs, application-defined event handlers are called in the event thread. Most GUI frameworks are single-threaded subsystems, so the main event loop is effectively still present, but it runs in its own thread under the control of the GUI toolkit rather than the application.

### Safety hazards

Because threads share the same memory address space and run concurrently, they can access or modify variables that other threads might be using.

Allowing multiple threads to access and modify the same variables introduces an element of non sequentiality into an otherwise sequential programming model, which can be confusing and difficult to reason about. For a multithreaded program's behavior to be predictable, access to shared variables must be properly coordinated so that threads do not interfere with one another. Fortunately, Java provides synchronization mechanisms to coordinate such access.

## Chapter 2: Thread Safety

Writing thread-safe code is, at its core, about managing access to state, and in particular to shared, mutable state. Informally, an object's state is its data, stored in state variables such as instance or static fields. An object's state may include fields from other, dependent objects; a HashMap's state is partially stored in the HashMap object itself, but also in many Map.Entry objects. An object's state encompasses any data that can affect its externally visible behavior. By shared, we mean that a variable could be accessed by multiple threads; by mutable, we mean that its value could change during its lifetime. What we are really trying to do is protect data from uncontrolled concurrent access. Whether an object needs to be thread-safe depends on whether it will be accessed from multiple threads. Making an object thread-safe requires using synchronization to coordinate access to its mutable state; failing to do so could result in data corruption and other undesirable consequences.

The primary mechanism for synchronization in Java is the synchronized keyword, which provides exclusive locking, but the term "synchronization" also includes the use of volatile variables, explicit locks, and atomic variables.

In a large program, identifying whether multiple threads might access a given variable can be complicated. The Java language doesn't force you to encapsulate state. But the better encapsulated your program state, the easier it is to make your program thread-safe and to help maintainers keep it that way.

A class is thread-safe when it continues to behave correctly when accessed from multiple threads. No set of operations performed sequentially or concurrently on instances of a thread-safe class can cause an instance to be in an invalid state.

### Example: a stateless servelet

```java
@ThreadSafe
public class StatelessFactorizer implements Servlet {
    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        encodeIntoResponse(resp, factors);
    }
}
```

StatelessFactorizer is, like most servlets, stateless: it has no fields and references no fields from other classes. The transient state for a particular computation exists solely in local variables that are stored on the thread's stack and are accessible only to the executing thread. One thread accessing a StatelessFactorizer cannot influence the result of another thread accessing the same StatelessFactorizer; because the two threads do not share state, it is as if they were accessing different instances. Since the actions of a thread accessing a stateless object cannot affect the correctness of operations in other threads, stateless objects are threadsafe.

### Atomicity

```java
// Servlet that counts requests without the necessary synchronization. Don't do this.

@NotThreadSafe
public class UnsafeCountingFactorizer implements Servlet {
    private long count = 0;

    public long getCount() { return count; }

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        ++count;
        encodeIntoResponse(resp, factors);
    }
}
```

UnsafeCountingFactorizer is not thread-safe, even though it would work just fine in a single-threaded environment. Just like UnsafeSequence on page 6, it is susceptible to lost updates. While the increment operation, ++count, may look like a single action because of its compact syntax, it is not atomic, which means that it does not execute as a single, indivisible operation. Instead, it is a shorthand for a sequence of three discrete operations: fetch the current value, add one to it, and write the new value back. This is an example of a read-modify-write operation, in which the resulting state is derived from the previous state.

The possibility of incorrect results in the presence of unlucky timing is so important in concurrent programming that it has a name: **_a race condition_**.

### Example: race conditions in lazy initialization

A common idiom that uses check-then-act is lazy initialization. The goal of lazy initialization is to defer initializing an object until it is actually needed while at the same time ensuring that it is initialized only once.

```java
// Race condition in lazy initialization. Don't do this.

@NotThreadSafe
public class LazyInitRace {
    private ExpensiveObject instance = null;

    public ExpensiveObject getInstance() {
        if (instance == null)
            instance = new ExpensiveObject();
        return instance;
    }
}
```

Say that threads A and B execute getInstance at the same time. A sees that instance is null, and instantiates a new ExpensiveObject. If instance is null when B examines it, the two callers to getInstance may receive two different results, even though getInstance is always supposed to return the same instance.

### Compound actions

Both LazyInitRace and UnsafeCountingFactorizer contained a sequence of operations that needed to be atomic, or indivisible, so we can ensure that other threads can observe or modify the state only before we start or after we finish, but not in the middle.

To ensure thread safety, check-then-act operations (like lazy initialization) and read-modify-write operations (like increment) must always be atomic. We refer collectively to check-then-act and read-modify-write sequences as compound actions: sequences of operations that must be executed atomically in order to remain thread-safe.

```java
// Servlet that counts requests using AtomicLong.

@ThreadSafe
public class CountingFactorizer implements Servlet {
    private final AtomicLong count = new AtomicLong(0);

    public long getCount() { return count.get(); }

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        count.incrementAndGet();
        encodeIntoResponse(resp, factors);
    }
}
```

The java.util.concurrent.atomic package contains atomic variable classes for effecting atomic state transitions on numbers and object references. Because the state of the servlet is the state of the counter and the counter is thread-safe, our servlet is once again thread-safe.

### Locking

Imagine that we want to improve the performance of our servlet by caching the most recently computed result, just in case two consecutive clients request factorization of the same number.

Don't do this.

```java
// Servlet that attempts to cache its last result without adequate atomicity.

@NotThreadSafe
public class UnsafeCachingFactorizer implements Servlet {
    private final AtomicReference<BigInteger> lastNumber
        = new AtomicReference<BigInteger>();
    private final AtomicReference<BigInteger[]> lastFactors
        = new AtomicReference<BigInteger[]>();

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);

        if (i.equals(lastNumber.get()))
            encodeIntoResponse(resp, lastFactors.get());
        else {
            BigInteger[] factors = factor(i);
            lastNumber.set(i);
            lastFactors.set(factors);
            encodeIntoResponse(resp, factors);
        }
    }
}
```

When multiple variables participate in an invariant, they are not independent: the value of one constrains the allowed value(s) of the others. Thus when updating one, you must update the others in the same atomic operation.

Using atomic references, we cannot update both lastNumber and lastFactors simultaneously, even though each call to set is atomic; there is still a window of vulnerability when one has been modified and the other has not. Similarly, the two values cannot be fetched simultaneously: between the time when thread A fetches the two values, thread B could have changed them, and again A may observe that the invariant does not hold.

### Intrinsic locks

Java provides a built-in locking mechanism for enforcing atomicity: the synchronized block. A synchronized block has two parts: a reference to an object that will serve as the lock, and a block of code to be guarded by that lock. A synchronized method is a shorthand for a synchronized block that spans an entire method body, and whose lock is the object on which the method is being invoked. (Static synchronized methods use the Class object for the lock.)

```java
synchronized (lock) {
    // Access or modify shared state guarded by lock
}
```

These built-in locks are called intrinsic locks or monitor locks. The lock is automatically acquired by the executing thread before entering a synchronized block and automatically released when control exits the synchronized block, whether by the normal control path or by throwing an exception out of the block.

Intrinsic locks in Java act as mutexes (or mutual exclusion locks), which means that at most one thread may own the lock. When thread A attempts to acquire a lock held by thread B, A must wait, or block, until B releases it. If B never releases the lock, A waits forever. Since only one thread at a time can execute a block of code guarded by a given lock, the synchronized blocks guarded by the same lock execute atomically with respect to one another.

SynchronizedFactorizer is now thread-safe; however, this approach is fairly extreme, since it inhibits multiple clients from using the factoring servlet simultaneously at all--resulting in unacceptably poor responsiveness.

```java
// Servlet that caches last result, but with unnacceptably poor concurrency.

@ThreadSafe
public class SynchronizedFactorizer implements Servlet {
    @GuardedBy("this") private BigInteger lastNumber;
    @GuardedBy("this") private BigInteger[] lastFactors;

    public synchronized void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);

        if (i.equals(lastNumber))
            encodeIntoResponse(resp, lastFactors);
        else {
            BigInteger[] factors = factor(i);
            lastNumber = i;
            lastFactors = factors;
            encodeIntoResponse(resp, factors);
        }
    }
}
```

### Reentrancy

Intrinsic locks are reentrant, if a thread tries to acquire a lock that it already holds, the request succeeds. Without reentrant locks, the very natural-looking code, in which a subclass overrides a synchronized method and then calls the superclass method, would deadlock:

```java
// Code that would deadlock if intrinsic locks were not reentrant.

public class Widget {
    public synchronized void doSomething() {
        ...
    }
}

public class LoggingWidget extends Widget {
    public synchronized void doSomething() {
        System.out.println(toString() + ": calling doSomething");
        super.doSomething();
    }
}
```

#### Guarding state with locks

Compound actions on shared state, such as incrementing a hit counter (read-modify- write) or lazy initialization (check-then-act), must be made atomic to avoid race conditions. Holding a lock for the entire duration of a compound action can make that compound action atomic. However, just wrapping the compound action with a synchronized block is not sufficient; if synchronization is used to coordinate access to a variable, it is needed everywhere that variable is accessed. Further, when using locks to coordinate access to a variable, the same lock must be used wherever that variable is accessed.

Acquiring the lock associated with an object does not prevent other threads from accessing that object--the only thing that acquiring a lock prevents any other thread from doing is acquiring that same lock.

A common locking convention is to encapsulate all mutable state within an object and to protect it from concurrent access by synchronizing any code path that accesses mutable state using the object's intrinsic lock.

It is also easy to subvert this locking protocol accidentally by adding a new method or code path and forgetting to use synchronization. Not all data needs to be guarded by locks--only mutable data that will be accessed from multiple threads.

When a variable is guarded by a lock--meaning that every access to that variable is performed with that lock held--you've ensured that only one thread at a time can access that variable. When a class has invariants that involve more than one state variable, there is an additional requirement: each variable participating in the invariant must be guarded by the same lock.

Merely synchronizing every method, as Vector does, is not enough to render compound actions on a Vector atomic:

```java
if (!vector.contains(element))
    vector.add(element);
```

This attempt at a put-if-absent operation has a race condition, even though both contains and add are atomic. While synchronized methods can make individual operations atomic, additional locking is required when multiple operations are combined into a compound action.

### Liveness and performance

Fortunately, it is easy to improve the concurrency of the servlet while maintaining thread safety by narrowing the scope of the synchronized block. It is reasonable to try to exclude from synchronized blocks long-running operations that do not affect shared state.

The portions of code that are outside the synchronized blocks operate exclusively on local (stack-based) variables, which are not shared across threads and therefore do not require synchronization.

```java
// Servlet that caches its last request and result.

@ThreadSafe
public class CachedFactorizer implements Servlet {
    @GuardedBy("this") private BigInteger lastNumber;
    @GuardedBy("this") private BigInteger[] lastFactors;
    @GuardedBy("this") private long hits;
    @GuardedBy("this") private long cacheHits;

    public synchronized long getHits() { return hits; }

    public synchronized double getCacheHitRatio() {
        return (double) cacheHits / (double) hits;
    }

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = null;

        synchronized (this) {
            ++hits;
            if (i.equals(lastNumber)) {
                ++cacheHits;
                factors = lastFactors.clone();
            }
        }

        if (factors == null) {
            factors = factor(i);
            synchronized (this) {
                lastNumber = i;
                lastFactors = factors.clone();
            }
        }
        encodeIntoResponse(resp, factors);
    }
}
```

The restructuring of CachedFactorizer provides a balance between simplicity (synchronizing the entire method) and concurrency (synchronizing the shortest possible code paths).
