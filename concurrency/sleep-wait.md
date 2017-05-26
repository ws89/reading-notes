# Object.java

``` java
public final void wait() throws InterruptedException
public final void wait(long timeout)
public final void wait(long timeout, int nanos)
```

- Causes the current thread to wait until another thread invokes the notify() method or the notifyAll() method for this object.

- **The current thread must own this object's monitor**. The thread releases ownership of this monitor and waits until another thread notifies threads waiting on this object's monitor to wake up either through a call to the notify method or the notifyAll method. The thread then waits until it can re-obtain ownership of the monitor and **resumes execution**. 

``` java
public final void notify()
```

- Wakes up a single thread that is waiting on this object's monitor. If any threads are waiting on this object, one of them is chosen to be awakened. The choice is arbitrary and occurs at the discretion of the implementation. 
- The awakened thread will **not be able to proceed** until the current thread relinquishes the lock on this object.
- The awakened thread will compete in the usual manner with any other threads that might be
   actively competing to synchronize on this object.
- **This method should only be called by a thread that is the owner of this object's monitor**. 

``` java
public final void notifyAll()
```

- Wakes up all threads that are waiting on this object's monitor. 

# Thread.java

```java
public static void sleep(long millis) throws InterruptedException
public static void sleep(long millis, int nanos) throws InterruptedException
```

- Causes the currently executing thread to sleep (**temporarily cease execution**) for the specified number of milliseconds.
- **The thread does not lose ownership of any monitors**.



# TestCode

``` java
@Test
public void testWait() throws InterruptedException {
  Object o = new Object();
  synchronized(o){
    System.out.println("before wait().");
    o.wait();
    System.out.println("after wait(). If this thread would not be awakened,this line would not execute.");
  }
}
```

``` java
@Test
public void testWait2() throws InterruptedException {
  Object o = new Object();
  synchronized(o){
    System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
    System.out.println("before wait().");
    o.wait(1000*5);
    System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
    System.out.println("after wait(). This thread would  be awakened in timeout period,then this line would resume execution.");
  }
}

/**
* output >>>

2017-03-29 21:37:02
before wait().
2017-03-29 21:37:07
after wait(). This thread would  be awakened in timeout period,then this line would resume execution.
*/
```

- The thread then waits until it can re-obtain ownership of the monitor and **resumes execution**.

# References

- [Object.java](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait())
- [Thread.java](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html#sleep(long))
- [Difference between wait() and sleep()](https://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep)

------

created at 2017-03-29 19:50