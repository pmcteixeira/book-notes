# Java theory and practice: Fixing the Java Memory Model, Part 1

by Brian Goetz

[Article](https://www.ibm.com/developerworks/java/library/j-jtp02244/index.html) | ![Part 2](/java_theory_and_practice_fixing_the_java_memory_model_part_2.markdown) | ![Part 3]()

--------------------------------------------------------------------------------

Not all multiprocessor systems exhibit cache coherency; if one processor has an updated value of a variable in its cache, but one which has not yet been flushed to main memory, other processors may not see that update. In the absence of cache coherency, two different processors may see two different values for the same location in memory. This may sound scary, but it is by design -- it is a means of obtaining higher performance and scalability -- but it places a burden on developers and compilers to create code that accommodates these issues.

## What is a memory model, and why do I need one?

A memory model describes the relationship between variables in a program (instance fields, static fields, and array elements) and the low-level details of storing them to and retrieving them from memory in a real computer system. Objects are ultimately stored in memory, but the compiler, runtime, processor, or cache may take some liberties with the timing of moving values to or from a variable's assigned memory location.

For example, a compiler may choose to optimize a loop index variable by storing it in a register, or the cache may delay flushing a new value of a variable to main memory until a more opportune time. All of these optimizations are to aid in higher performance, and are generally transparent to the user, but on multiprocessor systems, these complexities may sometimes show through.

The JMM allows the compiler and cache to take significant liberties with the order in which data is moved between a processor-specific cache (or register) and main memory, unless the programmer has explicitly asked for certain visibility guarantees using `synchronized` or `volatile`. This means that in the absence of synchronization, memory operations can appear to happen in different orders from the perspective of different threads.

## Shortcomings of the original JMM

The old JMM allowed some surprising and confusing things to happen, such as final fields appearing not to have the value that was set in the constructor (thus making supposedly immutable objects not immutable) and unexpected results with memory operation reordering.

### Synchronization and visibility (semantics)

Most programmers know that the synchronized keyword enforces a mutex (mutual exclusion) that prevents more than one thread at a time from entering a synchronized block protected by a given monitor. But **synchronization also has another aspect: It enforces certain memory visibility rules as specified by the JMM. It ensures that caches are flushed when exiting a synchronized block and invalidated when entering one, so that a value written by one thread during a synchronized block protected by a given monitor is visible to any other thread executing a synchronized block protected by that same monitor**. It also ensures that the compiler does not move instructions from inside a synchronized block to outside (although it can in some cases move instructions from outside a synchronized block inside). The JMM does not make this guarantee in the absence of synchronization.

### Original JMM Problem #1: Immutable objects weren't

One of the most surprising failings of the JMM is that immutable objects, whose immutability was intended to be guaranteed through use of the final keyword, could appear to change their value. (Public Service Reminder: Making all fields of an object final does not necessarily make the object immutable -- all fields must also be primitive types or references to immutable objects.) Immutable objects, like String, are supposed to require no synchronization. However, because of potential delays in propagating changes in memory writes from one thread to another, there is a possible race condition that would allow a thread to first see one value for an immutable object, and then at some later time see a different value.

### Original JMM Problem #2: Reordering volatile and nonvolatile stores

The other major area where the existing JMM caused some very confusing results was with memory operation reordering on volatile fields. The existing JMM says that volatile reads and writes are to go directly to main memory, prohibiting caching values in registers and bypassing processor-specific caches. This allows multiple threads to always see the most up-to-date value for a given variable. However, it turns out that this definition of volatile was not as useful as first intended, and resulted in significant confusion on the actual meaning of volatile.

**Using a volatile field as a "guard" variable:**

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

The idea here is that the volatile variable initialized acts as a guard to indicate that a set of other operations have completed. It's a good idea, but under the old JMM it didn't work, because the old JMM allowed nonvolatile writes (such as the write to the configOptions field, as well as the writes to the fields of the Map referenced by configOptions) to be reordered with volatile writes. So another thread might see initialized as true, but not yet have a consistent or current view of the field configOptions or the objects it references. The old semantics of volatile only made promises about the visibility of the variable being read or written, and no promises about other variables. While this approach was easier to implement efficiently, it turned out to be less useful than initially thought.

## Goals of JSR 133

JSR 133, chartered to fix the JMM. The final formal semantics are more complicated than originally expected, and in fact took quite a different form than initially envisioned, but the informal semantics are clear and intuitive and will be outlined in Part 2 of this article.
