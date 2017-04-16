# Overview

Four different degrees of reference strength: strong, soft, weak, and phantom, in order from strongest to weakest. 

### SoftReference

- Soft references are most often used to implement memory-sensitive caches.
- All soft references to softly-reachable objects are guaranteed to have been cleared before the virtual machine throws an `OutOfMemoryError`. 
- `SoftReferences` aren't *required* to behave any differently than `WeakReferences`, but in practice softly reachable objects are generally retained as long as memory is in plentiful supply.

### WeakReference

- A *weak reference*, simply put, is a reference that isn't strong enough to force an object to remain in memory. 


- An object which is only weakly reachable (the strongest references to it are `WeakReferences`) will be discarded at the next garbage collection cycle.
- `SoftReferences` aren't *required* to behave any differently than `WeakReferences`, but in practice softly reachable objects are generally retained as long as memory is in plentiful supply.

### PhantomReference

- You can't even retrieve the object -- its `get()` method always returns `null`. 
- The only use for such a reference is keeping track of when it gets enqueued into a `ReferenceQueue`, as at that point you know the object to which it pointed is dead. 

# Using

**Example**

```java
@Test
public void test(){
    Integer i = 1;
    WeakReference<Integer> weakReference = new WeakReference<Integer>(i);
    System.out.println(weakReference.get());//output:1
}
```



> java.lang.ThreadLocal.ThreadLocalMap.Entry

``` java
static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
  
  		/*
  		...
  		*/
}
```



# Differences

> WeakReference

```java
Counter counter = new Counter(); // strong reference - line 1
WeakReference<Counter> weakCounter = new WeakReference<Counter>(counter); //weak reference
counter = null; // now Counter object is eligible for garbage collection
```

Now as soon as you make strong reference `counter = null`, counter object created on line 1 becomes eligible for garbage collection; because it doesn't have any more Strong reference and Weak reference by reference variable `weakCounter` can not prevent `Counter` object from being garbage collected.

```java
Counter prime = new Counter();  // prime holds a strong reference - line 2
SoftReference<Counter> soft = new SoftReference<Counter>(prime) ; //soft reference variable has SoftReference to Counter Object created at line 2

prime = null;  // now Counter object is eligible for garbage collection but only be collected when JVM absolutely needs memory
```

Had this been Soft Reference, Counter object is not garbage collected **until JVM absolutely needs memory**. After making strong reference `null`, `Counter` object created on line 2 only has one soft reference **which can not prevent it from being garbage collected but it can delay collection, which is eager in case of `WeakReference`.**


# ReferenceQueue

```java
ReferenceQueue refQueue = new ReferenceQueue(); //reference will be stored in this queue for cleanup

DigitalCounter digit = new DigitalCounter();
PhantomReference<DigitalCounter> phantom = new PhantomReference<DigitalCounter>(digit, refQueue);
```

You can supply a `ReferenceQueue` instance while creating any `WeakReference`, `SoftReference` or `PhantomReference`.

Reference of instance will be appended to `ReferenceQueue` and you can use it to perform any clean-up by polling `ReferenceQueue`. 

# An Object's life-cycle diagram

![](/attachments/Weak-Strong-Soft-and-Phantom-Reference-in-Java.jpg)



# References

[Difference between WeakReference vs SoftReference vs PhantomReference vs Strong reference in Java](http://javarevisited.blogspot.com/2014/03/difference-between-weakreference-vs-softreference-phantom-strong-reference-java.html)

[Understanding Weak References Blog](https://community.oracle.com/blogs/enicholas/2006/05/04/understanding-weak-references)

[Class SoftReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/SoftReference.html)

[Class WeakReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/WeakReference.html)

[Class PhantomReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/PhantomReference.html)



---

created at 2017-04-15 22:53