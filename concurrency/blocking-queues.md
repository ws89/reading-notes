# Overview

A blocking queue is a queue that blocks when you try to dequeue from it and the queue is empty, or if you try to enqueue items to it and the queue is already full.A thread trying to dequeue from an empty queue is blocked until some other thread inserts an item into the queue. A thread trying to enqueue an item in a full queue is blocked until some other thread makes space in the queue, either by dequeuing one or more items or clearing the queue completely.

![A BlockingQueue with one thread putting into it, and another thread taking from it.](http://tutorials.jenkov.com/images/java-concurrency-utils/blocking-queue.png)

## Blocking Queue Implementation

```java
public class BlockingQueue {

  private List queue = new LinkedList();
  private int  limit = 10;

  public BlockingQueue(int limit){
    this.limit = limit;
  }

  public synchronized void enqueue(Object item)
  throws InterruptedException  {
    while(this.queue.size() == this.limit) {
      wait();
    }
    if(this.queue.size() == 0) {
      notifyAll();
    }
    this.queue.add(item);
  }

  public synchronized Object dequeue()
  throws InterruptedException{
    while(this.queue.size() == 0){
      wait();
    }
    if(this.queue.size() == this.limit){
      notifyAll();
    }
    return this.queue.remove(0);
  }

}
```

Notice how `notifyAll()` is only called from `enqueue()` and `dequeue()` if the queue size is equal to the size bounds (0 or limit). If the queue size is not equal to either bound when `enqueue()` or `dequeue()` is called, there can be no threads waiting to either enqueue or dequeue items.



---

# BlockingQueue

## BlockingQueue Methods

A `BlockingQueue` has 4 different sets of methods for inserting, removing and examining the elements in the queue. Each set of methods behaves differently in case the requested operation cannot be carried out immediately. Here is a table of the methods:

|             | **Throws Exception** | **Special Value** | **Blocks** | **Times Out**                 |
| ----------- | -------------------- | ----------------- | ---------- | ----------------------------- |
| **Insert**  | `add(o)`             | `offer(o)`        | `put(o)`   | `offer(o, timeout, timeunit)` |
| **Remove**  | `remove(o)`          | `poll()`          | `take()`   | `poll(timeout, timeunit)`     |
| **Examine** | `element()`          | `peek()`          |            |                               |

The 4 different sets of behaviour means this:

1. **Throws Exception**: 
   If the attempted operation is not possible immediately, an exception is thrown.
2. **Special Value**: 
   If the attempted operation is not possible immediately, a special value is returned (often true / false).
3. **Blocks**: 
   If the attempted operation is not possible immedidately, the method call blocks until it is.
4. **Times Out**: 
   If the attempted operation is not possible immedidately, the method call blocks until it is, but waits no longer than the given timeout. Returns a special value telling whether  the operation succeeded or not (typically true / false). 

It is not possible to insert `null` into a `BlockingQueue`. If you try to insert null, the    `BlockingQueue` will throw a `NullPointerException`.



## Implementations

### ArrayBlockingQueue

`ArrayBlockingQueue` is **a bounded, blocking queue** that stores the elements internally **in an array**.That it is bounded means that it cannot store unlimited amounts of elements. There is an upper bound on the number of elements it can store at the same time. You set the upper bound at instantiation time, and **after that it cannot be changed**.

The `ArrayBlockingQueue` stores the elements internally in **FIFO (First In, First Out)** order.The `head` of the queue is the element which has been in queue the longest time, and the `tail` of the queue is the element which has been in the queue the shortest time.

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<String>(1024);
queue.put("1");
String string = queue.take();
```



### DelayQueue

The `DelayQueue` blocks the elements internally until a certain delay has expired.The elements must implement the interface `java.util.concurrent.Delayed`. Here is how the interface looks:

```java
public interface Delayed extends Comparable<Delayed> {
 public long getDelay(TimeUnit timeUnit);
}
```

The value returned by the `getDelay()` method should be the delay remaining before this element can be released. If 0 or a negative value is returned, the delay will be considered expired, and the element released at the next `take()` etc. call on the `DelayQueue`.

The `Delayed` interface also extends the `java.lang.Comparable` interface, as you can see, which means that `Delayed` objects can be compared to each other. This is probably used internally in the `DelayQueue` to order the elements in the queue, so they are released ordered by their expiration time.

```java
@Test
public void delayQueue() throws InterruptedException {

    BlockingQueue<DelayImplementation> delayQueue = new DelayQueue<>();

    DelayImplementation delayed = new DelayImplementation(3000);

    delayQueue.put(delayed);

    System.out.println( new SimpleDateFormat("HH:mm:ss").format(new Date()));//2017-04-02 18:04:18

    Thread.sleep(2000);

    delayQueue.take().print();//2017-04-02 18:04:21

}
```

```java
class DelayImplementation implements Delayed{

    private long duration = 0;

    public DelayImplementation(long duration){
        this.duration = duration + System.currentTimeMillis();
    }

    @Override
    public int compareTo(Delayed o) {
        return (int) ( this.duration - ((DelayImplementation)o).getDuration() );
    }

    @Override
    public long getDelay(TimeUnit unit) {
        long currentMillis = System.currentTimeMillis();
        long diff = this.duration - currentMillis;

        System.out.println( duration );
        System.out.println( currentMillis );
        System.out.println( diff );//execute more times
        return TimeUnit.SECONDS.toSeconds( diff );
    }

    public void print(){
        System.out.println( new SimpleDateFormat("HH:mm:ss").format(new Date()));
    }
  
  	public long getDuration() {
      return duration;
    }
}
```



### LinkedBlockingQueue

The `LinkedBlockingQueue` keeps the elements internally in a linked structure (linked nodes). This linked structure can optionally have an upper bound if desired. If no upper bound is specified,    `Integer.MAX_VALUE` is used as the upper bound.

The `LinkedBlockingQueue` stores the elements internally in FIFO (First In, First Out) order. 

```java
BlockingQueue<String> unbounded = new LinkedBlockingQueue<String>();
BlockingQueue<String> bounded   = new LinkedBlockingQueue<String>(1024);

bounded.put("Value");

String value = bounded.take();
```



### PriorityBlockingQueue

The `PriorityBlockingQueue` is an unbounded concurrent queue. It uses the same ordering rules as the `java.util.PriorityQueue` class. You cannot insert null into this queue.

All elements inserted into the `PriorityBlockingQueue` must implement the `java.lang.Comparable` interface. The elements thus order themselves according to whatever priority you decide in your `Comparable` implementation.

Notice that the `PriorityBlockingQueue` does not enforce any specific behaviour for elements that have equal priority (compare() == 0).

Also notice, that in case you obtain an `Iterator` from a `PriorityBlockingQueue`, the  `Iterator` does not guarantee to iterate the elements in priority order.

```java
@Test
public void priorityQueue() throws InterruptedException {
    BlockingQueue<PriorityImplementation> blockingQueue = new PriorityBlockingQueue();

    blockingQueue.put(new PriorityImplementation(3,"P3"));
    blockingQueue.put(new PriorityImplementation(1,"P1"));
    blockingQueue.put(new PriorityImplementation(2,"P2"));

    blockingQueue.take().print();
    blockingQueue.take().print();
    blockingQueue.take().print();
    /*
        P3 3
        P2 2
        P1 1
     */
}
```



```java
class PriorityImplementation implements Comparable<PriorityImplementation>{
    int priority = 0;
    String name = null;

    public PriorityImplementation(int priority,String name){
        this.priority = priority;
        this.name = name;
    }

    @Override
    public int compareTo(PriorityImplementation o) {
        return o.priority - this.priority;
    }

    void print(){
        System.out.println( this.name + "\t" + this.priority );
    }
}
```



### SynchronousQueue

The `SynchronousQueue` is a queue that can only contain a single element internally. A thread inseting an element into the queue is blocked until another thread takes that element from the queue. Likewise, if a thread tries to take an element and no element is currently present, that thread is blocked until a thread insert an element into the queue.

Calling this class a queue is a bit of an overstatement. It's more of a rendesvouz point.



The SynchronousQueue is more of a handoff, whereas the LinkedBlockingQueue just allows a single element.  The difference being that the put() call to a SynchronousQueue *will not return* until there is a corresponding take() call, but with a LinkedBlockingQueue of size 1, the put() call (to an empty queue) will return immediately. 

I can't say that i have ever used the SynchronousQueue directly myself, but it is the default BlockingQueue used for the `Executors.newCachedThreadPool()` methods.  It's essentially the BlockingQueue implementation for when you don't *really* want a queue (you don't want to maintain any pending data).



# BlockingDeque

A `deque` is short for "Double Ended Queue". Thus, a `deque` is a queue which you can insert and take elements from, from both ends.

A `BlockingDeque` could be used if threads are both producing and consuming elements of the same queue. It could also just be used if the producting thread needs to insert at both ends of the queue, and the consuming thread needs to remove from both ends of the queue.

![A BlockingDeque - threads can put and take from both ends of the deque.](http://tutorials.jenkov.com/images/java-concurrency-utils/blocking-deque.png)

## BlockingDeque methods

A `BlockingDeque` has 4 different sets of methods for inserting, removing and examining the elements in the deque. Each set of methods behaves differently in case the requested operation cannot be carried out immediately.

|             | **Throws Exception** | **Special Value** | **Blocks**     | **Times Out**                      |
| ----------- | -------------------- | ----------------- | -------------- | ---------------------------------- |
| **Insert**  | `addFirst(o)`        | `offerFirst(o)`   | `putFirst(o)`  | `offerFirst(o, timeout, timeunit)` |
| **Remove**  | `removeFirst(o)`     | `pollFirst(o)`    | `takeFirst(o)` | `pollFirst(timeout, timeunit)`     |
| **Examine** | `getFirst(o)`        | `peekFirst(o)`    |                |                                    |

|             | **Throws Exception** | **Special Value** | **Blocks**    | **Times Out**                     |
| ----------- | -------------------- | ----------------- | ------------- | --------------------------------- |
| **Insert**  | `addLast(o)`         | `offerLast(o)`    | `putLast(o)`  | `offerLast(o, timeout, timeunit)` |
| **Remove**  | `removeLast(o)`      | `pollLast(o)`     | `takeLast(o)` | `pollLast(timeout, timeunit)`     |
| **Examine** | `getLast(o)`         | `peekLast(o)`     |               |                                   |



## BlockingDeque Extends BlockingQueue

The `BlockingDeque` interface extends the `BlockingQueue` interface. That means that you can use a `BlockingDeque` as a `BlockingQueue`. If you do so, the various inserting methods will add the elements to the end of the deque, and the removing methods will remove the elements from the beginning of the deque. The inserting and removing methods of the `BlockingQueue` interface, that is.

Here is a table of what the methods of the `BlockingQueue` does in a `BlockingDeque` implementation:

| **BlockingQueue** | **BlockingDeque** |
| ----------------- | ----------------- |
| add()             | addLast()         |
| offer() x 2       | offerLast() x 2   |
| put()             | putLast()         |
|                   |                   |
| remove()          | removeFirst()     |
| poll() x 2        | pollFirst()       |
| take()            | takeFirst()       |
|                   |                   |
| element()         | getFirst()        |
| peek()            | peekFirst()       |




## Implementations

### LinkedBlockingDeque

The word `Deque` comes from the term "Double Ended Queue". A `Deque` is thus a queue where you can insert and remove elements from both ends of the queue.

```java
BlockingDeque<String> deque = new LinkedBlockingDeque<String>();

deque.addFirst("1");
deque.addLast("2");

String two = deque.takeLast();
String one = deque.takeFirst();
```



# References

[Blocking Queues](http://tutorials.jenkov.com/java-concurrency/blocking-queues.html)

[BlockingQueue](http://tutorials.jenkov.com/java-util-concurrent/blockingqueue.html)

[ArrayBlockingQueue](http://tutorials.jenkov.com/java-util-concurrent/arrayblockingqueue.html)

[DelayQueue](http://tutorials.jenkov.com/java-util-concurrent/delayqueue.html)

[How do i set up a DelayQueue's Delay](https://stackoverflow.com/questions/23219801/how-do-i-set-up-a-delayqueues-delay)

[LinkedBlockingQueue](http://tutorials.jenkov.com/java-util-concurrent/linkedblockingqueue.html)

[PriorityBlockingQueue](http://tutorials.jenkov.com/java-util-concurrent/priorityblockingqueue.html)

[SynchronousQueue](http://tutorials.jenkov.com/java-util-concurrent/synchronousqueue.html)

[When should I use SynchronousQueue](https://stackoverflow.com/questions/8591610/when-should-i-use-synchronousqueue)

[BlockingDeque](http://tutorials.jenkov.com/java-util-concurrent/blockingdeque.html)

[LinkedBlockingDeque](http://tutorials.jenkov.com/java-util-concurrent/linkedblockingdeque.html)

---

created at 2017-06-02 13:41