# Overview

The purpose of thread signaling is to enable threads to send signals to each other. Additionally, thread signaling enables threads to wait for signals from other threads. For instance, a thread B might wait for a signal from thread A indicating that data is ready to be processed. 



# Signaling via Shared Objects

A simple way for threads to send signals to each other is by setting the signal values in some shared object variable.Thread A may set the boolean member variable hasDataToProcess to true from inside a synchronized block, and thread B may read the hasDataToProcess member variable, also inside a synchronized block. Here is a simple example of an object that can hold such a signal, and provide methods to set and check it:

```java
public class MySignal{

  protected boolean hasDataToProcess = false;

  public synchronized boolean hasDataToProcess(){
    return this.hasDataToProcess;
  }

  public synchronized void setHasDataToProcess(boolean hasData){
    this.hasDataToProcess = hasData;  
  }

}
```



# Busy Wait

Thread B which is to process the data is waiting for data to become available for processing.In other words, it is waiting for a signal from thread A which causes hasDataToProcess() to return true. Here is the loop that thread B is running in, while waiting for this signal:

```java
protected MySignal sharedSignal = ...

...

while(!sharedSignal.hasDataToProcess()){
  //do nothing... busy waiting
}
```

Notice how the while loop keeps executing until hasDataToProcess() returns true. This is called busy waiting.The thread is busy while waiting.

**Busy waiting is not a very efficient utilization of the CPU** in the computer running the waiting thread,except if the average waiting time is very small. Else, it would be smarter if **the waiting thread could somehow sleep or become inactive until it receives the signal it is waiting for.**



# wait(), notify() and notifyAll()

Java has a builtin wait mechanism that **enable threads to become inactive while waiting for signals**. The class java.lang.Object defines three methods, wait(), notify(), and notifyAll(), to facilitate this.

A thread that calls wait() on any object becomes inactive until another thread calls notify() on that object. In order to call either wait() or notify the calling thread must first obtain the lock on that object. In other words, **the calling thread must call wait() or notify() from inside a synchronized block**. 

```java
public class MonitorObject{
}

public class MyWaitNotify{

  MonitorObject myMonitorObject = new MonitorObject();

  public void doWait(){
    synchronized(myMonitorObject){
      try{
        myMonitorObject.wait();
      } catch(InterruptedException e){...}
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      myMonitorObject.notify();
    }
  }
}
```

A thread cannot call wait(), notify() or notifyAll() without holding the lock on the object the method is called on. If it does, an **llegalMonitorStateException** is thrown.

**Once a thread is awakened it cannot exit the wait() call until the thread calling notify() has left its synchronized block.**

The awakened thread must reobtain the lock on the monitor object before it can exit the wait() call, because the wait call is nested inside a synchronized block. If multiple threads are awakened using notifyAll() only one awakened thread at a time can exit the wait() method, since each thread must obtain the lock on the monitor object in turn before exiting wait().



# Missed Signals

The methods notify() and notifyAll() do not save the method calls to them in case no threads are waiting when they are called. The notify signal is then just lost. 

To avoid losing signals they should be stored inside the signal class. In the MyWaitNotify example the notify signal should be stored in a member variable inside the MyWaitNotify instance.

```java
public class MyWaitNotify2{

  MonitorObject myMonitorObject = new MonitorObject();
  boolean wasSignalled = false;

  public void doWait(){
    synchronized(myMonitorObject){
      if(!wasSignalled){
        try{
          myMonitorObject.wait();
         } catch(InterruptedException e){...}
      }
      //clear signal and continue running.
      wasSignalled = false;
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      wasSignalled = true;
      myMonitorObject.notify();
    }
  }
}
```

Notice how the doNotify() method now sets the wasSignalled variable to true before calling notify(). Also, notice how the doWait() method now checks the wasSignalled variable before calling wait(). In fact it only calls wait() if no signal was received in between the previous doWait() call and this.



# Spurious Wakeups

For inexplicable reasons it is possible for threads to wake up even if notify() and notifyAll() has not been called. This is known as spurious wakeups. 

If a spurious wakeup occurs in the MyWaitNofity2 class's doWait() method the waiting thread may continue processing without having received a proper signal to do so! This could cause serious problems in your application.

To guard against spurious wakeups the signal member variable is checked **inside a while loop instead of inside an if-statement**. Such a while loop is also called a **spin lock**. The thread awakened spins around until the condition in the spin lock (while loop) becomes false.

```java
public class MyWaitNotify3{

  MonitorObject myMonitorObject = new MonitorObject();
  boolean wasSignalled = false;

  public void doWait(){
    synchronized(myMonitorObject){
      while(!wasSignalled){
        try{
          myMonitorObject.wait();
         } catch(InterruptedException e){...}
      }
      //clear signal and continue running.
      wasSignalled = false;
    }
  }

  public void doNotify(){
    synchronized(myMonitorObject){
      wasSignalled = true;
      myMonitorObject.notify();
    }
  }
}
```



# Don't call wait() on constant String's or global objects

![Calling wait()/notify() on string constants](http://tutorials.jenkov.com/images/java-concurrency/strings-wait-notify.png)



Don't use global objects, string constants etc. for wait() / notify() mechanisms. Use an object that is unique to the construct using it. For instance, each MyWaitNotify3 (example from earlier sections) instance has its own MonitorObject instance rather than using the empty string for wait() / notify() calls.




# Practice - wait() & notifyAll()

```java
@Test
public void waitNotify() throws InterruptedException {

    class WaitNotify{
        public synchronized void waitM() throws InterruptedException {
            System.out.println( Thread.currentThread().getName() + "wait...");
            this.wait();
            System.out.println( Thread.currentThread().getName() + "awaken...");
        }

        public synchronized void notifyM(){
            System.out.println( Thread.currentThread().getName() + "notifyAll");
            this.notifyAll();//if use notify(),only one thread will be awaken.
        }
    }

    WaitNotify waitNotifyObject = new WaitNotify();

    Runnable waitRunnable = new Runnable() {
        @Override
        public void run() {
            try {
                waitNotifyObject.waitM();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    };

    Runnable notifyRunnable = new Runnable() {
        @Override
        public void run() {
            waitNotifyObject.notifyM();
        }
    };

    Thread wait1 = new Thread(waitRunnable,"[wait-thread-1]");
    Thread wait2 = new Thread(waitRunnable,"[wait-thread-2]");

    Thread notify = new Thread(notifyRunnable,"[notifyAll-thread]");

    wait1.start();
    wait2.start();
    notify.start();

    wait1.join();
    wait2.join();
    notify.join();

    System.out.println("done!");
    /*
        [wait-thread-1]wait...
        [wait-thread-2]wait...
        [notifyAll-thread]notifyAll
        [wait-thread-2]awaken...
        [wait-thread-1]awaken...
        done!
     */
}
```



# References

[Thread Signaling](http://tutorials.jenkov.com/java-concurrency/thread-signaling.html)



---

created at 2017-05-27 14:29