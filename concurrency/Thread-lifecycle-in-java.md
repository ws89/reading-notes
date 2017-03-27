## Thread.State

- [`NEW`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#NEW)
  ​     A thread that has not yet started is in this state.
- [`RUNNABLE`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#RUNNABLE)
  ​     A thread executing in the Java virtual machine is in this state.
- [`BLOCKED`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#BLOCKED)
  ​     A thread that is blocked waiting for a monitor lock
  ​     is in this state.
- [`WAITING`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#WAITING)
  ​     A thread that is waiting indefinitely for another thread to
  ​     perform a particular action is in this state.
- [`TIMED_WAITING`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#TIMED_WAITING)
  ​     A thread that is waiting for another thread to perform an action
  ​     for up to a specified waiting time is in this state.
- [`TERMINATED`](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html#TERMINATED)
  ​     A thread that has exited is in this state.

# Diagrams

![](http://javaconceptoftheday.com/wp-content/uploads/2014/11/JavaThreadLifeCycle.png)



![](http://www.javatpoint.com/images/threadstates.jpg)

# References

- [Thread Life Cycle OR Thread States In Java](http://javaconceptoftheday.com/thread-life-cycle-thread-states-java/)
- [Life cycle of a Thread (Thread States)](http://www.javatpoint.com/life-cycle-of-a-thread)
- [Enum Thread.State](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html)

-----

Created at 2017-03-27 20:50