# Non-blocking Algorithms

Non-blocking algorithms in the context of concurrency are algorithms that allows threads to access shared state (or otherwise collaborate or communicate) without blocking the threads involved. In more general terms, an algorithm is said to be non-blocking if the suspension of one thread cannot lead to the suspension of other threads involved in the algorithm.



## Blocking Concurrency Algorithms

A blocking concurrency algorithm is an algorithm which either:

- A: Performs the action requested by the thread - OR
- B: Blocks the thread until the action can be performed safely

Many types of algorithms and concurrent data structures are blocking. For instance, the different implementations of the [java.util.concurrent.BlockingQueue](http://tutorials.jenkov.com/java-util-concurrent/blockingqueue.html) interface are all blocking data structures. If a thread attempts to insert an element into a `BlockingQueue` and  the queue does not have space, the inserting thread is blocked (suspended) until the `BlockingQueue` has space for the new element.

![The behaviour of a blocking algorithm guarding a shared data structure.](http://tutorials.jenkov.com/images/java-concurrency/non-blocking-algorithms-1.png)

## Non-blocking Concurrency Algorithms

A non-blocking concurrency algorithm is an algorithm which either:

- A: Performs the action requested by the thread - OR
- B: Notifies the requesting thread that the action could not be performed

Java contains several non-blocking data structures too. The [AtomicBoolean](http://tutorials.jenkov.com/java-util-concurrent/atomicboolean.html),    [AtomicInteger](http://tutorials.jenkov.com/java-util-concurrent/atomicinteger.html), [AtomicLong](http://tutorials.jenkov.com/java-util-concurrent/atomiclong.html)    and [AtomicReference](http://tutorials.jenkov.com/java-util-concurrent/atomicreference.html) are all examples of non-blocking data structures.

![The behaviour of a non-blocking algorithm guarding a shared data structure.](http://tutorials.jenkov.com/images/java-concurrency/non-blocking-algorithms-2.png)

## Non-blocking vs Blocking Algorithms

Blocking algorithms block the thread until the requested action can be performed. Non-blocking algorithms notify the thread requesting the action that the action cannot be performed.



## Optimistic Locking With Compare and Swap

```java
import java.util.concurrent.atomic.AtomicLong;

public class AtomicCounter {
    private AtomicLong count = new AtomicLong(0);

    public void inc() {
        boolean updated = false;
        while(!updated){
            long prevCount = this.count.get();
            updated = this.count.compareAndSet(prevCount, prevCount + 1);
        }
    }

    public long count() {
        return this.count.get();
    }
}
```

This version is just as thread-safe as the previous version. What is interesting about this version is the    implementation of the `inc()` method. The `inc()` method no longer contains a synchronized    block. Instead it contains these lines:

```java
boolean updated = false;
while(!updated){
    long prevCount = this.count.get();
    updated = this.count.compareAndSet(prevCount, prevCount + 1);
}
```

These lines are not an atomic operation. That means, that it is possible for two different threads to call    the `inc()` method and execute the `long prevCount = this.count.get()` statement, and thus    both obtain the previous count for the counter. Yet, the above code does not contain any race conditions.

The secret is in the second of the two lines inside the `while` loop. The `compareAndSet()`    method call is an atomic operation. It compares the internal value of the `AtomicLong` to an expected    value, and if the two values are equal, sets a new internal value for the `AtomicLong`. **The    `compareAndSet()` method is typically supported by compare-and-swap instructions directly in the CPU.** Therefore no synchronization is necessary, and no thread suspension is necessary. This saves the thread suspension overhead.

Imagine that the internal value of the `AtomicLong` is 20. Then two threads read that value, and both  tries to call `compareAndSet(20, 20 + 1)`. Since `compareAndSet()` is an atomic operation, the threads will execute this method sequentially (one at a time).

The first thread will compare the expected value of 20 (the previous value of the counter) to the internal value of the `AtomicLong`. Since the    two values are equal, the `AtomicLong` will update its internal value to 21 (20 + 1). The  `updated` variable will be set to `true` and the `while` loop will stop.

Now the second thread calls `compareAndSet(20, 20 + 1)`. Since the internal value of the `AtomicLong`    is no longer 20, this call will fail. The internal value of the `AtomicLong` will not be set to 21.    The `updated` variable will be set to `false`, and the thread will spin one more time around    the `while` loop. This time it will read the value 21 and attempt to update it to 22. If no other thread has called  `inc()` in the meantime, the second iteration will succeed in updating the `AtomicLong` to 22.



### Why is it Called Optimistic Locking?

The code shown in the previous section is called ***optimistic locking***. Optimistic locking is different from traditional locking, sometimes also called ***pessimistic locking***. Traditional locking blocks the access to the shared memory with a synchronized block or a lock of some kind. A synchronized block or lock may result in threads being suspended.

Optimistic locking allows all threads to create a copy of the shared memory without any blocking. The threads may then make modifications to their copy, and attempt to write their modified version back into the shared memory. If no other thread has made any modifications to the shared memory, the compare-and-swap  operation allows the thread to write its changes to shared memory. If another thread has already changed the  shared memory, the thread will have to obtain a new copy, make its changes and attempt to write them to shared    memory again.

The reason this is called optimistic locking is that threads obtain a copy of the data they want to change and apply their changes, under the optimistic assumption that no other thread will have made changes to the shared memory in the meantime. When this optimistic assumption holds true, the thread just managed to update shared memory without locking. When this assumption is false, the work was wasted, but still    no locking was applied.

Optimistic locking tends to work best with **low to medium contention on the shared memory**. If the content is very high on the shared memory, threads will **waste a lot of CPU cycles** copying and modifying the shared memory only to fail writing the changes back to the shared memory. But, if you have a lot of content on shared memory, you should anyways consider redesigning your code to lower the contention.

**The optimistic locking mechanism I have shown here is non-blocking.**



## Non-swappable Data Structures

Imagine if the shared data structure is a queue. Each thread trying to either insert or take elements from the    queue would have to copy the whole queue and make the desired modifications to the copy. This could be achieved via    an `AtomicReference`. Copy the reference, copy and modify the queue, and try to swap the reference pointed    to in the `AtomicReference` to the newly created queue.

However, ***a big data structure* may require a lot of memory and CPU cycles to copy.** This will make your application    spend a lot more memory, and waste a lot of time on the copying. This will impact the performance of your application, especially if contention on the data structure is high. Furthermore, the longer time it takes for a    thread to copy and modify the data structure, the bigger the probability is that some other thread will have modified    the data structure in between. As you know, if another thread has modified the shared data structure since it was    copied, all other threads have to restart their copy-modify operations. This will increase the impact on performance    and memory consumption even more.



## Sharing Intended Modifications

Instead of copying and modifying the whole shared data structure, a thread can share its *intended modification* of the shared data structure. The process for a thread wanting to make a modification to the shared data structure then becomes:

1. Check if another thread has submitted an intended modification to the data structure.
2. If no other thread has submitted an intended modification, create an intended modification (represented by an object)  and submit that intended modification to the data structure (using a compare-and-swap operation).    
3. Carry out the modification of the shared data structure.    
4. Remove the reference to the intended modification to signal to other threads that the intended modification  has been carried out.    

As you can see, the second step can block other threads from submitting an intended modification. Thus, the second step effectively works as a lock of the shared data structure. If one thread successfully submits an  intended modification, no other thread can submit an intended modification until the first intended modification    is carried out.

If a thread submits an intended modification and then gets blocked doing some other work, the shared data structure is effectively locked. The shared data structure does not directly block the other threads using the data structure. The other threads can detect that they cannot submit an intended modification and decide to something else. Obviously, we need to fix that.

### Completable Intended Modifications

**To avoid that a submitted intended modification can lock the shared data structure, a submitted intended modification  object must contain enough information for another thread to complete the modification.** Thus, if the thread submitting the intended modification never completes the modification, **another thread can complete the modification on its behalf**,and keep the shared data structure available for other threads to use.

![A non-blocking algorithm blueprint using completable intended modifications](http://tutorials.jenkov.com/images/java-concurrency/non-blocking-algorithms-5.png)

The modifications must be carried out as one or more compare-and-swap operations. Thus, if two threads try to complete the intended modification, only one thread will be able to carry out any of the compare-and-swap operations. As soon as a compare-and-swap operation has been completed, further attempts to complete that compare-and-swap operation will fail.



## The A-B-A Problem

The above illustrated algorithm can suffer from the A-B-A problem. The A-B-A problem refers to the situation where    a variable is changed from A to B and then back to A again. For another thread it is thus not possible to detect    that the variable was indeed changed.

If thread A checks for ongoing updates, copies data and is suspended by the thread scheduler, a thread B may    be able to access the shared data structure in the meanwhile. If thread B performs a full update of the data structure,    and removes its intended modification, it will look to thread A as if no modification has taken place since it    copied the data structure. However, a modification did take place. When thread A continues to perform its update    based on its now out-of-date copy of the data structure, the data structure will have thread B's modification undone.

![The A-B-A problem which can occur with completable intended modifications.](http://tutorials.jenkov.com/images/java-concurrency/non-blocking-algorithms-6.png)

### A-B-A Solutions

A common solution to the A-B-A problem is to not just swap a pointer to an intended modification object, but    to combine the pointer with a counter, and swap pointer + counter using a single compare-and-swap operation. This is possible in languages that support pointers like C and C++. Thus, even if the current modification pointer    is set back to point to "no ongoing modification", the counter part of the pointer + counter will have    been incremented, making the update visible to other threads.

In Java you cannot merge a reference and a counter together into a single variable. Instead Java provides the [`AtomicStampedReference`](http://tutorials.jenkov.com/java-util-concurrent/atomicstampedreference.html) class    which can swap a reference and a stamp atomically using a compare-and-swap operation.

## 

## The Benefit of Non-blocking Algorithms

### Choice

The first benefit of non-blocking algorithms is, that threads are given a choice about what to do when their    requested action cannot be performed. Instead of just being blocked, the request thread has a choice about what    to do. Sometimes there is nothing a thread can do. In that case it can choose to block or wait itself, thus    freeing up the CPU for other tasks. But at least the requesting thread is given a choice.

### No Deadlocks

The second benefit of non-blocking algorithms is, that the suspension of one thread cannot lead to the suspension    of other threads. This means that deadlock cannot occur. Two threads cannot be blocked waiting for each other to    release a lock they want. Since threads are not blocked when they cannot perform their requested action, they    cannot be blocked waiting for each other. Non-blocking algorithms may still result in live lock, where two threads    keep attempting some action, but keep being told that it is not possible (because of the actions of the other thread).

### No Thread Suspension

Suspending and reactivating a thread is costly. Yes, the costs of suspension and reactivation has gone down over    time as operating systems and thread libraries become more efficient. However, there is still a high price to    pay for thread suspension and reactivation.

Whenever a thread is blocked it is suspended, thus incurring the overhead of thread suspension and reactivation.  Since threads are not suspended by non-blocking algorithms, this overhead does not occur. This means that the    CPUs can potentially spend more time performing actual business logic instead of context switching.

On a multi CPU system blocking algorithms can have more significant impact on the overall performance.  A thread running on CPU A can be blocked waiting for a thread running on CPU B. This lowers the level of parallelism    the application is capable of achieving. Of course, CPU A could just schedule another thread to run,  but suspending and activating threads (context switches) are expensive. The less threads need to be suspended  the better.

### Reduced Thread Latency

Latency in this context means the time between a requested action becomes possible and the thread actually    performs it. Since threads are not suspended in non-blocking algorithms they do not have to pay the expensive, slow    reactivation overhead. That means that when a requested action becomes possible threads can respond faster and    thus reduce their response latency.

The non-blocking algorithms often obtain the lower latency by busy-waiting until the requested action becomes    possible. Of course, in a system with high thread contention on the non-blocking data structure, CPUs may end up    burning a lot of cycles during these busy waits. This is a thing to keep in mind. Non-blocking algorithms may    not be the best if your data structure has high thread contention. However, there are often ways do redesign your    application to have less thread contention.



# References

[Non-blocking Algorithms](http://tutorials.jenkov.com/java-concurrency/non-blocking-algorithms.html)

---

created at 2017-06-05 15:36