# Java theory and practice: Fixing the Java Memory Model, Part 2

by Brian Goetz

[Article](https://www.ibm.com/developerworks/library/j-jtp03304/) | [Part 1](java_theory_and_practice_fixing_the_java_memory_model_part_1.markdown) | [Part 3]()

--------------------------------------------------------------------------------

While the Java platform included support for threading from the outset, including a cross-platform memory model that was intended to provide "Write Once, Run Anywhere" guarantees for properly synchronized programs, the original memory model had some holes. JSR 133 was formed, charged with fixing the Java Memory Model.

## Visibility, revisited

One of the key concepts needed to understand the JMM is that of visibility -- how do you know that if thread A executes someVariable = 3, other threads will see the value 3 written there by thread A? A number of reasons exist for why another thread might not immediately see the value 3 for someVariable: it could be because the compiler has reordered instructions in order to execute more efficiently, or that someVariable was cached in a register, or that its value was written to the cache on the writing processor but not yet flushed to main memory, or that there is an old (or stale) value in the reading processor's cache. It is the memory model that determines when a thread can reliably "see" writes to variables made by other threads. In particular, the memory model defines semantics for `volatile`, `synchronized`, and `final` that make guarantees of visibility of memory operations across threads.

When a thread exits a synchronized block as part of releasing the associated monitor, the JMM requires that the local processor cache be flushed to main memory. (Actually, the memory model does not talk about caches -- it talks about an abstraction, **local memory**, which encompasses caches, registers, and other hardware and compiler optimizations.) Similarly, as part of acquiring the monitor when entering a synchronized block, local caches are invalidated so that subsequent reads will go directly to main memory and not the local cache. This process guarantees that when a variable is written by one thread during a synchronized block protected by a given monitor and read by another thread during a synchronized block protected by the same monitor, the write to the variable will be visible by the reading thread. The JMM does not make this guarantee in the absence of synchronization -- which is why synchronization (or its younger sibling, `volatile`) must be used whenever multiple threads are accessing the same variables.

## New guarantees for volatile

The original semantics for `volatile` guaranteed only that reads and writes of `volatile` fields would be made directly to main memory, instead of to registers or the local processor cache, and that actions on volatile variables on behalf of a thread are performed in the order that the thread requested. In other words, this means that the old memory model made promises only about the visibility of the variable being read or written, and no promises about the visibility of writes to other variables. While this was easier to implement efficiently, it turned out to be less useful than initially thought.

While reads and writes of volatile variables could not be reordered with reads and writes of other volatile variables, they could still be reordered with reads and writes of nonvolatile variables. In [Part 1](java_theory_and_practice_fixing_the_java_memory_model_part_1.markdown), you learned how the code in Listing 1 was not sufficient (under the old memory model) to guarantee that the correct value for `configOptions` and all the variables reachable indirectly through `configOptions` (such as the elements of the `Map`) would be visible to thread B, because the initialization of `configOptions` could have been reordered with the `initialization` of the volatile `initialized` variable.

**Listing 1:**

```java
Map configOptions;
char[] configText;
volatile boolean initialized = false;

...

// In thread A
configOptions = new HashMap();
configText = readConfigFile(fileName);
processConfigOptions(configText, configOptions);
initialized = true;

...

// In thread B
while (!initialized)
  sleep();
// use configOptions
```

Under the new memory model, when thread A writes to a volatile variable V, and thread B reads from V, any variable values that were visible to A at the time that V was written are guaranteed now to be visible to B. The result is a more useful semantics of `volatile`, at the cost of a somewhat higher performance penalty for accessing volatile fields.

## What happens before what?

Actions, such as reads and writes of variables, are ordered within a thread according to what is called the "program order" -- the order in which the semantics of the program say they should occur. (The compiler is actually free to take some liberties with the program order within a thread as long as as-if-serial semantics are preserved.) Actions in different threads are not necessarily ordered with respect to each other at all -- if you start two threads and they each execute without synchronizing on any common monitors or touching any common volatile variables, you can predict exactly nothing about the relative order in which actions in one thread will execute (or become visible to a third thread) with respect to actions in the other thread.

Additional ordering guarantees are created when a thread is started, a thread is joined with another thread, a thread acquires or releases a monitor (enters or exits a synchronized block), or a thread accesses a volatile variable. The JMM describes the ordering guarantees that are made when a program uses synchronization or volatile variables to coordinate activities in multiple threads. The new JMM, informally, defines an ordering called `happens-before`, which is a partial ordering of all actions within a program, as follows:

- Each action in a thread `happens-before` every action in that thread that comes later in the program order
- An unlock on a monitor `happens-before` every subsequent lock on that same monitor
- A write to a volatile field `happens-before` every subsequent read of that same volatile
- A call to `Thread.start()` on a thread `happens-before` any actions in the started thread
- All actions in a thread `happen-before` any other thread successfully returns from a `Thread.join()` on that thread

It is the third of these rules, the one governing reads and writes of volatile variables, that is new and fixes the problem with the example in Listing 1\. Because the write of the volatile variable `initialized` happens after the initialization of `configOptions`, the use of `configOptions` happens after the read of `initialized`, and the read of initialized happens after the write to `initialized`, you can conclude that the initialization of `configOptions` by thread A happens before the use of `configOptions` by thread B. Therefore, configOptions and the variables reachable through it will be visible to thread B.

## Initialization safety

The new JMM also seeks to provide a new guarantee of **initialization safety** -- that as long as an object is properly constructed (meaning that a reference to the object is not published before the constructor has completed), then all threads will see the values for its final fields that were set in its constructor, regardless of whether or not synchronization is used to pass the reference from one thread to another. Further, any variables that can be reached through a final field of a properly constructed object, such as fields of an object referenced by a final field, are also guaranteed to be visible to other threads as well. This means that if a final field contains a reference to, say, a `LinkedList`, in addition to the correct value of the reference being visible to other threads, also the contents of that `LinkedList` at construction time would be visible to other threads without synchronization. The result is a significant strengthening of the meaning of `final` -- that final fields can be safely accessed without synchronization, and that compilers can assume that final fields will not change and can therefore optimize away multiple fetches.

## Final means final

The mechanism by which final fields could appear to change their value under the old memory model was outlined in [Part 1](java_theory_and_practice_fixing_the_java_memory_model_part_1.markdown) -- in the absence of synchronization, another thread could first see the default value for a final field and then later see the correct value.

Under the new memory model, there is something similar to a happens-before relationship between the write of a final field in a constructor and the initial load of a shared reference to that object in another thread. When the constructor completes, all of the writes to final fields (and to variables reachable indirectly through those final fields) become "frozen," and any thread that obtains a reference to that object after the freeze is guaranteed to see the frozen values for all frozen fields. Writes that initialize final fields will not be reordered with operations following the freeze associated with the constructor.
