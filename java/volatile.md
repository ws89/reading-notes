# Overview

The Java `volatile` keyword is used to mark a Java variable as "being stored in main memory". More precisely that means, that every read of a volatile variable will be **read from the computer's main memory, and not from the CPU cache**, and that every write to a volatile variable will be written to main memory, and not just to the CPU cache.



# Visibility

The Java `volatile` keyword **guarantees visibility of changes to variables across threads**. 

> In a multithreaded application where the threads operate on non-volatile variables, each thread may copy variables from main memory into a CPU cache while working on them, for performance reasons. If your computer contains more than one CPU, each thread may run on a different CPU. That means, that each thread may copy the variables into the CPU cache of different CPUs. 

![Threads may hold copies of variables from main memory in CPU caches.](http://tutorials.jenkov.com/images/java-concurrency/java-volatile-1.png)

```java
public class SharedObject {
    public int counter = 0;
}
```

if use non-volatile variables , that will be **invisiable with CPU cache and main memery** 

![he CPU cache used by Thread 1 and main memory contains different values for the counter variable.](http://tutorials.jenkov.com/images/java-concurrency/java-volatile-2.png)

By declaring the `counter` variable `volatile` all writes to the `counter` variable will be written back to main memory immediately. Also, all reads of the `counter` variable will be read directly from main memory. 

```java
public class SharedObject {
    public volatile int counter = 0;
}
```



# Happens-Before Guarantee

- If Thread A writes to a volatile variable and Thread B subsequently reads the same volatile variable, then all variables visible to Thread A *before* writing the volatile variable, will also be visible to Thread B *after* it has read the volatile variable.
- **The reading and writing instructions of volatile variables cannot be reordered by the JVM** (the JVM may reorder instructions for performance reasons as long as the JVM detects no change in program behaviour from the reordering). **Instructions before and after can be reordered, but the volatile read or write cannot be mixed with these instructions.** Whatever instructions follow a read or write of a volatile variable are guaranteed to happen after the read or write.

When a thread writes to a volatile variable, then not just the volatile variable itself is written to main memory. Also all other variables changed by the thread before writing to the volatile variable are also flushed to main memory. When a thread reads a volatile variable it will also read all other variables from main memory which were flushed to main memory together with the volatile variable.

**Example:**

```java
Thread A:
    sharedObject.nonVolatile = 123;
    sharedObject.counter     = sharedObject.counter + 1;
Thread B:
    int counter     = sharedObject.counter;
    int nonVolatile = sharedObject.nonVolatile;
```

> Since Thread A writes the non-volatile variable `sharedObject.nonVolatile` before writing to the volatile    `sharedObject.counter`, then both `sharedObject.nonVolatile` and `sharedObject.counter`    are written to main memory when Thread A writes to `sharedObject.counter` (the `volatile` variable).

>   Since Thread B starts by reading the volatile `sharedObject.counter`, then both the `sharedObject.counter`    and `sharedObject.nonVolatile` are read from main memory into the CPU cache used by Thread B.    By the time Thread B reads `sharedObject.nonVolatile` it will see the value written by Thread A.



**Example:**

```java
public class Exchanger {
    private Object   object       = null;
    private volatile hasNewObject = false;

    public void put(Object newObject) {
        wile(hasNewObject) {
            //wait - do not overwrite existing new object
        }
        object = newObject;
        hasNewObject = true; //volatile write
    }

    public Object take(){
        while(!hasNewObject){ //volatile read
            //wait - don't take old object (or null)
        }
        Object obj = object;
        hasNewObject = false; //volatile write
        return obj;
    }
}
```

> Thread A may be putting objects from time to time by calling `put()`. Thread B may take objects from time to time by calling `take()`. This `Exchanger` can work just fine using a `volatile` variable (without the use of `synchronized` blocks), as long as only Thread A calls `put()` and only Thread B calls `take()`.



**Example:**

"The happens before guarantee" guarantees that read and write instructions of `volatile` variables cannot be reordered. Instructions before and after can be reordered, but the volatile read/write instruction cannot be reordered with any instruction occurring before or after it.

```java
sharedObject.nonVolatile1 = 123;
sharedObject.nonVolatile2 = 456;
sharedObject.nonVolatile3 = 789;

sharedObject.volatile     = true; //a volatile variable

int someValue1 = sharedObject.nonVolatile4;
int someValue2 = sharedObject.nonVolatile5;
int someValue3 = sharedObject.nonVolatile6;
```

> The JVM may reorder the first 3 instructions, as long as all of them *happens before* the  `volatile` write instruction (they must all be executed before the volatile write instruction).
>
> Similarly, the JVM may reorder the last 3 instructions as long as the volatile write instruction *happens before* all of them. None of the last 3 instructions can be reordered to before the volatile write instruction.
>
> That is basically the meaning of the Java volatile happens before guarantee.



# Not Enough

As soon as a thread needs to first read the value of a `volatile` variable, and based on that value generate a new value for the shared `volatile` variable, a `volatile` variable is no longer enough to guarantee correct visibility. 



> If Thread 1 reads a shared `counter` variable with the value 0 into its CPU cache, increment it to 1 and not write the changed value back into main memory. Thread 2 could then read the same `counter` variable from main memory where the value of the variable is still 0, into its own CPU cache. Thread 2 could then also increment    the counter to 1, and also not write it back to main memory. 

![Two threads have read a shared counter variable into their local CPU caches and incremented it.](http://tutorials.jenkov.com/images/java-concurrency/java-volatile-3.png)

> Thread 1 and Thread 2 are now practically out of sync. The real value of the shared `counter` variable should have been 2, but each of the threads has the value 1 for the variable in their CPU caches, and in main memory the value is still 0. It is a mess! Even if the threads eventually write their value for the shared `counter` variable back to main memory, the value will be wrong.

# Performance

Reading from and writing to main memory is more expensive than accessing the CPU cache. Accessing volatile variables also prevent instruction reordering which is a normal performance enhancement technique. Thus, you should only use volatile variables when you really need to enforce visibility of variables.


# References

[Java Volatile Keyword](http://tutorials.jenkov.com/java-concurrency/volatile.html)



---

created at 2017-04-13 21:30