# Java Memory Model

![The Java Memory Model From a Logic Perspective](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-1.png)

### Thread Stack

- Each thread running in the Java virtual machine has its own thread stack.
- The thread stack also contains all local variables for each method being executed (all methods on the call stack).
- A thread can only access it's own thread stack. Even if two threads are executing the exact same code, the two threads will still create the local variables of that code in each their own thread stack.
- All local variables of primitive types( `boolean`, `byte`, `short`, `char`, `int`, `long`, `float`, `double`)  are fully stored on the thread stack and are thus not visible to other threads.



### Heap

- The heap contains all objects created in your Java application, regardless of what thread created the object.
- This includes the object versions of the primitive types(e.g. `Byte`, `Integer`, `Long` etc.).



![The Java Memory Model showing where local variables and objects are stored in memory.](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-2.png)

### Storage

- A local variable may be of a primitive type, in which case it is totally kept on the thread stack.
- A local variable may also be a reference to an object. In that case the reference (the local variable) is stored on the thread stack,but the object itself if stored on the heap.
- An object may contain methods and these methods may contain local variables. These local variables are also stored on the thread stack,even if the object the method belongs to is stored on the heap.
- An object's member variables are stored on the heap along with the object itself. That is true both when the member variable is of a primitive type, and if it is a reference to an object.
- Static class variables are also stored on the heap along with the class definition.



# Hardware Memory Architecture

![Modern hardware memory architecture.](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-4.png)

- Each CPU contains a set of registers which are essentially in-CPU memory.
- The CPU can perform operations much faster on these registers than it can perform on variables in main memory.
- Each CPU may also have a CPU cache memory layer.In fact, most modern CPUs have a cache memory layer of some size.
- The CPU can access its cache memory much faster than main memory, but typically not as fast as it can access its internal registers.
- Some CPUs may have multiple cache layers (Level 1 and Level 2), but this is not so important to know to understand how the Java memory model interacts with memory. What matters is to know that CPUs can have a cache memory layer of some sort.
- A computer also contains a main memory area (RAM). All CPUs can access the main memory. The main memory area is typically much bigger than the cache memories of the CPUs.
- Typically, when a CPU needs to access main memory it will read part of main memory into its CPU cache. It may even read part of the cache into its internal registers and then perform operations on it. When the CPU needs to write the result back to main memory it will flush the value from its internal register to the cache memory,and at some point flush the value back to main memory.
- The CPU cache can have data written to part of its memory at a time, and flush part of its memory at a time. It does not have to read / write the full cache each time it is updated. Typically the cache is updated in smaller memory blocks called "cache lines".One or more cache lines may be read into the cache memory, and one or mor cache lines may be flushed back to main memory again.




![The division of thread stack and heap among CPU internal registers, CPU cache and main memory.](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-5.png)



# Visibility of Shared Objects

If two or more threads are sharing an object, without the proper use of either **`volatile`** declarations or **synchronization**, updates to the shared object made by one thread may not be visible to other threads.

![Visibility Issues in the Java Memory Model.](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-6.png)

One thread running on the left CPU copies the shared object into its CPU cache, and changes its `count` variable to 2. This change is not visible to other threads running on the right CPU, because the update to `count` has not been flushed back to main memory yet.



# Race Conditions

Imagine if thread A reads the variable `count` of a shared object into its CPU cache. Imagine too,that thread B does the same, but into a different CPU cache. Now thread A adds one to `count`, and thread B does the same. Now `var1` has been incremented two times, once in each CPU cache.

The two increments have been carried out concurrently without proper synchronization. Regardless of which of thread A and B that writes its updated version of `count` back to main memory, the updated value will only be 1 higher than the original value, despite the two increments.

![Race Condition Issues in the Java Memory Model.](http://tutorials.jenkov.com/images/java-concurrency/java-memory-model-7.png)

To solve this problem you can use a **Java synchronized block**. A synchronized block guarantees that only one thread can enter a given critical section of the code at any given time. Synchronized blocks also guarantee that all variables accessed inside the synchronized block will be read in from main memory, and when the thread exits the synchronized block, all updated variables will be flushed back to main memory again, regardless of whether the variable is declared volatile or not.



# References

[Java Memory Model](http://tutorials.jenkov.com/java-concurrency/java-memory-model.html)



---

created at 2017-05-03 22:10