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

Example

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





# References

[Understanding Weak References Blog](https://community.oracle.com/blogs/enicholas/2006/05/04/understanding-weak-references)

[Class SoftReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/SoftReference.html)

[Class WeakReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/WeakReference.html)

[Class PhantomReference<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ref/PhantomReference.html)



---

created at 2017-04-15 22:53