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

`StatelessFactorizer` is, like most servlets, stateless: it has no fields and references no fields from other classes. The transient state for a particular computation exists solely in local variables that are stored on the thread's stack and are accessible only to the executing thread. One thread accessing a StatelessFactorizer cannot influence the result of another thread accessing the same `StatelessFactorizer`; because the two threads do not share state, it is as if they were accessing different instances. Since the actions of a thread accessing a stateless object cannot affect the correctness of operations in other threads, stateless objects are threadsafe.

### Atomicity

- Reads and writes are atomic for reference variables and for most primitive variables (all types except long and double).
- Reads and writes are atomic for all variables declared volatile (including long and double variables).

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

`UnsafeCountingFactorizer` is not thread-safe, even though it would work just fine in a single-threaded environment. Just like `UnsafeSequence`, it is susceptible to lost updates. While the increment operation, `++count`, may look like a single action because of its compact syntax, it is not atomic, which means that it does not execute as a single, indivisible operation. Instead, it is a shorthand for a sequence of three discrete operations: fetch the current value, add one to it, and write the new value back. This is an example of a **read-modify-write** operation, in which the resulting state is derived from the previous state.

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

Say that threads A and B execute getInstance at the same time. A sees that instance is null, and instantiates a new `ExpensiveObject`. If instance is null when B examines it, the two callers to getInstance may receive two different results, even though getInstance is always supposed to return the same instance.

### Compound actions

Both `LazyInitRace` and `UnsafeCountingFactorizer` contained a sequence of operations that needed to be atomic, or indivisible, so we can ensure that other threads can observe or modify the state only before we start or after we finish, but not in the middle.

To ensure thread safety, **check-then-act** operations (like lazy initialization) and **read-modify-write** operations (like increment) must always be atomic. We refer collectively to check-then-act and read-modify-write sequences as compound actions: sequences of operations that must be executed atomically in order to remain thread-safe.

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

The `java.util.concurrent.atomic` package contains atomic variable classes for effecting atomic state transitions on numbers and object references. Because the state of the servlet is the state of the counter and the counter is thread-safe, our servlet is once again thread-safe.

### Locking

Imagine that we want to improve the performance of our servlet by caching the most recently computed result, just in case two consecutive clients request factorization of the same number.

```java
// Servlet that attempts to cache its last result without adequate atomicity. Don't do this.

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

### Guarding state with locks

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

## Chapter 3: Sharing Objects

![Chapter 3](assets/chap3.png)

Synchronization also has another significant, and subtle, aspect, besides atomicity: memory visibility. We want to ensure that when a thread modifies the state of an object, other threads can actually see the changes that were made. Without synchronization, this may not happen.

### Visibility & Stale Data

If you write a value to a variable and later read that variable with no intervening writes, you can expect to get the same value back. But when the reads and writes occur in different threads, this is simply not the case. Insufficiently synchronized programs can cause **stale data**. Stale data can cause serious and confusing failures such as unexpected exceptions, corrupted data structures, inaccurate computations, and infinite loops.

```java
// Non-thread-safe mutable integer holder
@NotThreadSafe
public class MutableInteger {
    private int value;

    public int get() { return value; }
    public void set(int value) { this.value = value; }
}
```

```java
// Thread-safe mutable integer holder
@ThreadSafe
public class SynchronizedInteger {
    @GuardedBy("this") private int value;

    public synchronized int get() { return value; }
    public synchronized void set(int value) { this.value = value; }
}
```

#### Nonatomic 64-bit operations

The Java Memory Model requires fetch and store operations to be atomic, but for nonvolatile long and double variables, the JVM is permitted to treat a 64-bit read or write as two separate 32-bit operations. If the reads and writes occur in different threads, it is therefore possible to read a nonvolatile long and get back the high 32 bits of one value and the low 32 bits of another.

#### Locking and Visibility

**_When thread A executes a synchronized block, and subsequently thread B enters a synchronized block guarded by the same lock, the values of variables that were visible to A prior to releasing the lock are guaranteed to be visible to B upon acquiring the lock_**.

#### Volatile variables

The Java language also provides an alternative, weaker form of synchronization, **volatile variables**, to ensure that updates to a variable are propagated predictably to other threads. When a field is declared volatile, the compiler and runtime are put on notice that this variable is shared and that operations on it should not be reordered with other memory operations. **Volatile variables are not cached in registers or in caches where they are hidden from other processors, so a read of a volatile variable always returns the most recent write by any thread**.

Yet accessing a volatile variable performs no locking and so cannot cause the executing thread to block, making volatile variables a lighter-weight synchronization mechanism than synchronized. Relying too heavily on volatile variables for visibility is not recommended, as it is harder to understand than code that uses locking.

The most common use for a volatile variables is a completion, interruption, or status flag.

**LOCKING CAN GUARANTEE BOTH VISIBILITY AND ATOMICITY; VOLATILE VARIABLES CAN ONLY GUARANTEE VISIBILITY**.

You can use volatile variables only when all the following criteria are met:

- Writes to the variable do not depend on its current value, or you can ensure that only a single thread ever updates the value;
- The variable does not participate in invariants with other state variables; and
- Locking is not required for any other reason while the variable is being accessed.

### Publication and escape

Publishing internal state variables can compromise encapsulation and make it more difficult to preserve invariants; **An object that is published when it should not have been is said to have escaped**. Publishing an object means making it available to code outside of its current scope, such as:

- by storing a reference to it where other code can find it (e.g: store a reference in a public static field)

  ```java
  // Publishing an object
  public static Set<Secret> knownSecrets;

  public void initialize() {
        knownSecrets = new HashSet<Secret>();
  }
  ```

- returning a object reference from a non-private method. More generally, any object that is reachable from a published object by following some chain of non-private field references and method calls has also been published.

  ```java
  // Allowing internal mutable state to escape. DON'T DO THIS.
  class UnsafeStates {
    private String[] states = new String[] {
      "AK", "AL" ...
    };

    public String[] getStates() { return states; }
  }
  ```

- passing it to a method in another class.

  ```java
  // Implicitly allowing the this reference to escape. DON'T DO THIS.
  public class ThisEscape {
    public ThisEscape(EventSource source) {
        source.registerListener(
            new EventListener() {
                public void onEvent(Event e) {
                    doSomething(e);
                }
            });
    }
  }
  ```

When `ThisEscape` publishes the EventListener, it implicitly publishes the enclosing ThisEscape instance as well, because inner class instances contain a hidden reference to the enclosing instance.

#### Safe construction practices

`ThisEscape` illustrates an important special case of escape - when the `this` references escapes during construction. When the inner EventListener instance is published, so is the enclosing ThisEscape instance. But an object is in a predictable, consistent state only after its constructor returns, so publishing an object from within its constructor can publish an incompletely constructed object. **This is true even if the publication is the last statement in the constructor. If the this reference escapes during construction, the object is considered not properly constructed**.

A common mistake that can let the this reference escape during construction is to start a thread from a constructor. When an object creates a thread from its constructor, it almost always shares its this reference with the new thread, either explicitly (by passing it to the constructor) or implicitly (because the Thread or Runnable is an inner class of the owning object). The new thread might then be able to see the owning object before it is fully constructed. There's nothing wrong with creating a thread in a constructor, but it is best not to start the thread immediately.

Calling an overrideable instance method (one that is neither private nor final) from the constructor can also allow the this reference to escape. If you are tempted to register an event listener or start a thread from a constructor, you can avoid the improper construction by using a private constructor and a public factory method, as shown in `SafeListener`:

```java
/*
 * Using a factory method to prevent the this reference from escaping
 * during construction.
 */
public class SafeListener {
    private final EventListener listener;

    private SafeListener() {
        listener = new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        };
    }

    public static SafeListener newInstance(EventSource source) {
        SafeListener safe = new SafeListener();
        source.registerListener(safe.listener);
        return safe;
    }
}
```

### Thread confinement

Accessing shared, mutable data requires using synchronization; one way to avoid this requirement is to not share. If data is only accessed from a single thread, no synchronization is needed. This technique, thread confinement, is one of the simplest ways to achieve thread safety. When an object is confined to a thread, such usage is automatically thread-safe even if the confined object itself is not.

The language and core libraries provide mechanisms that can help in maintaining thread confinement - local variables and the ThreadLocal class - but even with these, it is still the programmer's responsibility to ensure that thread-confined objects do not escape from their intended thread.

#### Ad-hoc thread confinement

Ad-hoc thread confinement describes when the responsibility for maintaining thread confinement falls entirely on the implementation.

A special case of thread confinement applies to volatile variables. It is safe to perform read-modify-write operations on shared volatile variables as long as you ensure that the volatile variable is only written from a single thread. In this case, you are confining the modification to a single thread to prevent race conditions, and the visibility guarantees for volatile variables ensure that other threads see the most up-to-date value. Because of its fragility, ad-hoc thread confinement should be used sparingly; if possible, use one of the stronger forms of thread confinment (stack confinement or ThreadLocal) instead.

#### Stack confinement

Stack confinement is a special case of thread confinement in which an object can only be reached through local variables.

Local variables are intrinsically confined to the executing thread; they exist on the executing thread's stack, which is not accessible to other threads. Stack confinement (also called within-thread or thread-local usage, but not to be confused with the ThreadLocal library class) is simpler to maintain and less fragile than ad-hoc thread confinement. For primitively typed local variables, such as numPairs in `loadTheArk`, you cannot violate stack confinement even if you tried. There is no way to obtain a reference to a primitive variable, so the language semantics ensure that primitive local variables are always stack confined.

```java
// Thread confinement of local primitive and reference variables.
public int loadTheArk(Collection<animal> candidates) {
    SortedSet<animal> animals;
    int numPairs = 0;
    Animal candidate = null;

    // animals confined to method, don’t let them escape!
    animals = new TreeSet<animal>(new SpeciesGenderComparator());
    animals.addAll(candidates);

    for (Animal a : animals) {
        if (candidate == null || !candidate.isPotentialMate(a))
            candidate = a;
        else {
            ark.load(new AnimalPair(candidate, a));
            ++numPairs;
            candidate = null;
        }
    }
    return numPairs;
}
```

Maintaining stack confinement for object references requires a little more assistance from the programmer to ensure that the referent does not escape. In `loadTheArk`, we instantiate a `TreeSet` and store a reference to it in animals. At this point, there is exactly one reference to the Set, held in a local variable and therefore confined to the executing thread. However, if we were to publish a reference to the Set (or any of its internals), the confinement would be violated and the animals would escape. **Using a non-thread-safe object in a within-thread context is still thread-safe**.

#### ThreadLocal

ThreadLocal provides get and set accessor methods that maintain a separate copy of the value for each thread that uses it, so a get returns the most recent value passed to set from the currently executing thread.

By using a ThreadLocal to store the JDBC connection, as in `ConnectionHolder`, each thread will have its own connection.

```java
// Using ThreadLocal to ensure thread confinement.
private static ThreadLocal<connection> connectionHolder = new ThreadLocal<connection>() {
    public Connection initialValue() {
        return DriverManager.getConnection(DB_URL);
    }
};

public static Connection getConnection() {
    return connectionHolder.get();
}
```

When a thread calls `ThreadLocal.get` for the first time, `initialValue` is consulted to provide the initial value for that thread. The thread-specific values are stored in the Thread object itself; when the thread terminates, the thread-specific values can be garbage collected. If you are porting a single-threaded application to a multithreaded environment, you can preserve thread safety by converting shared global variables into ThreadLocals, if the semantics of the shared globals permits this; an application wide cache would not be as useful if it were turned into a number of thread-local caches.

### Immutability

The other end-run around the need to synchronize is to use immutable objects. If an object's state cannot be modified, these risks and complexities simply go away.

Neither the Java Language Specification nor the Java Memory Model formally defines immutability, but immutability is not equivalent to simply declaring all fields of an object final. **An object whose fields are all final may still be mutable, since final fields can hold references to mutable objects**.

An object is immutable if:

- Its state cannot be modified after construction;
- All its fields are final; and
- It is properly constructed (the `this` reference does not escape during construction).

Immutable objects can still use mutable objects internally to manage their state, as illustrated by `ThreeStooges`. While the `Set` that stores the names is mutable, the design of `ThreeStooges` makes it impossible to modify that Set after construction. The stooges reference is final, so all object state is reached through a final field. The last requirement, proper construction, is easily met since the constructor does nothing that would cause the this reference to become accessible to code other than the constructor and its caller.

```java
// Immutable class built out of mutable underlying objects
@Immutable
public final class ThreeStooges {
    private final Set<String> stooges = new HashSet<String>();

    public ThreeStooges() {
        stooges.add("Moe");
        stooges.add("Larry");
        stooges.add("Curly");
    }

    public boolean isStooge(String name) {
        return stooges.contains(name);
    }
}
```

#### Final fields

Final fields can't be modified **(although the objects they refer to can be modified if they are mutable)**, but they also have special semantics under the Java Memory Model. **It is the use of final fields that makes possible the guarantee of initialization safety that lets immutable objects be freely accessed and shared without synchronization**.

#### Example: Using volatile to publish immutable objects

Immutable objects can sometimes provide a weak form of atomicity. Race conditions in accessing or updating multiple related variables can be eliminated by using an immutable object to hold all the variables.

```java
// Immutable holder for caching a number and its factors.
@Immutable
class OneValueCache {
    private final BigInteger lastNumber;
    private final BigInteger[] lastFactors;

    public OneValueCache(BigInteger i, BigInteger[] factors) {
        lastNumber = i;
        lastFactors = Arrays.copyOf(factors, factors.length);
    }

    public BigInteger[] getFactors(BigInteger i) {
        if (lastNumber == null || !lastNumber.equals(i))
            return null;
        else
            return Arrays.copyOf(lastFactors, lastFactors.length);
    }
}
```

With a mutable holder object, you would have to use locking to ensure atomicity; with an immutable one, once a thread acquires a reference to it, it need never worry about another thread modifying its state. If the variables are to be updated, a new holder object is created, but any threads working with the previous holder still see it in a consistent state. When a thread sets the volatile cache field to reference a new OneValueCache, the new cached data becomes immediately visible to other threads.

```java
// Caching the last result using a volatile reference to a immutable holder object
@ThreadSafe
public class VolatileCachedFactorizer implements Servlet {
    private volatile OneValueCache cache = new OneValueCache(null, null);

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = cache.getFactors(i);

        if (factors == null) {
            factors = factor(i);
            cache = new OneValueCache(i, factors);
        }
        encodeIntoResponse(resp, factors);
    }
}
```

### Safe publication

Unfortunately, simply storing a reference to an object into a public field, is not enough to publish that object safely. This improper publication could allow another thread to observe a partially constructed object.

#### Improper publication: when good objects go bad

You cannot rely on the integrity of partially constructed objects. An observing thread could see the object in an inconsistent state, and then later see its state suddenly change, even though it has not been modified since publication.

```java
// Unsafe publication
// Publishing an object without adequate synchronization. Don’t do this.
public Holder holder;

public void initialize() {
    holder = new Holder(42);
}
```

```java
// Class at risk of failure if not properly published.
public class Holder {
    private int n;

    public Holder(int n) { this.n = n; }

    public void assertSanity() {
        if (n != n)
        throw new AssertionError("This statement is false.");
    }
}
```

Because synchronization was not used to make the Holder visible to other threads, we say the Holder was not properly published.

#### Immutable objects and initialization safety

Because immutable objects are so important, the Java Memory Model offers a special guarantee of initialization safety for sharing immutable objects. **As we've seen, that an object reference becomes visible to another thread does not necessarily mean that the state of that object is visible to the consuming thread**. In order to guarantee a consistent view of the object's state, synchronization is needed. Immutable objects, on the other hand, can be safely accessed even when synchronization is not used to publish the object reference. For this guarantee of initialization safety to hold, all of the requirements for immutability must be met: unmodifiable state, all fields are final, and proper construction.

Final fields can be safely accessed without additional synchronization. However, if final fields refer to mutable objects, synchronization is still required to access the state of the objects they refer to.

#### Safe publication idioms

To publish an object safely, both the reference to the object and the object's state must be made visible to other threads at the same time. A properly constructed object can be safely published by:

- Initializing an object reference from a static initializer;
- Storing a reference to it into a `volatile` field or `AtomicReference`;
- Storing a reference to it into a final field of a properly constructed object; or
- Storing a reference to it into a field that is properly guarded by a lock.

#### Effectively immutable objects

Objects that are not technically immutable, but whose state will not be modified after publication, are called effectively immutable. Using effectively immutable objects can simplify development and improve performance by reducing the need of synchronization.

#### Mutable objects

If an object may be modified after construction, safe publication ensures only the visibility of the as-published state. Synchronization must be used not only to publish a mutable object, but also every time the object is accessed to ensure visibility of subsequent modifications. To share mutable objects safely, they must be safely published and be either thread-safe or guarded by a lock.

The publication requirements for an object depend on its mutability:

- Immutable objects can be published through any mechanism.
- Effectively immutable objects must be safely published;
- Mutable objects must be safely published, and must be either thread-safe or guarded by a lock.

#### Sharing objects safely

The most useful policies for using and sharing objects in a concurrent program are:

- **Thread-confined**. A thread-confined object is owned exclusively by and confined to one thread, and can be modified by its owning thread.
- **Shared read-only**. A shared read-only object can be accessed concurrently by multiple threads without additional synchronization, but cannot be modified by any thread. Shared read-only objects include immutable and effectively immutable objects.
- **Shared thread-safe**. A thread-safe object performs synchronization internally, so multiple threads can freely access it through its public interface without further synchronization.
- **Guarded**. A guarded object can be accessed only with a specific lock held. Guarded objects include those that are encapsulated within other thread-safe objects and published objects that are known to be guarded by a specific lock.

### Articles

- [Volatile](http://tutorials.jenkov.com/java-concurrency/volatile.html) by Jenkov
- [Java theory and practice: Fixing the Java Memory Model, Part 1](/articles/java_theory_and_practice_fixing_the_java_memory_model_part_1.markdown)
- [Java theory and practice: Fixing the Java Memory Model, Part 2](/articles/java_theory_and_practice_fixing_the_java_memory_model_part_2.markdown)
