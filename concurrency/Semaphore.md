# Semaphore

The `java.util.concurrent.Semaphore` class is a [counting semaphore](http://tutorials.jenkov.com/java-concurrency/semaphores.html#counting). That means that it has two main methods:

- acquire()
- release()

The counting semaphore is initialized with a given number of "permits". For each call to `acquire()` a permit is taken by the calling thread. For each call to `release()` a permit is returned to the semaphore. Thus, at most N threads can pass the `acquire()` method without any `release()` calls, where N is the number of permits the semaphore was initialized with. The permits are just a simple counter. Nothing fancy here.

## Semaphore Usage

As semaphore typically has two uses:

1. To guard a critical section against entry by more than N threads at a time.
2. To send signals between two threads.



### Guarding Critical Sections

If you use a semaphore to guard a critical section, the thread trying to enter the critical section will typically first try to acquire a permit, enter the critical section, and then release the permit again after. Like this:

```java
Semaphore semaphore = new Semaphore(1);

//critical section
semaphore.acquire();

...

semaphore.release();
```

#### Practice

```java
/**
 * Created by WS on 2017/6/13.
 */
public class SemaphoreTest {

    @Test
    public void test() throws InterruptedException {
        Semaphore semaphore = new Semaphore(2);

        int i = 1;
        while ( i <= 5 ){
            new Thread( new SemaphoreRunnable(semaphore) , "Thread-"+i ).start();
            i++;
        }

        Thread.sleep(3000);
    }

    class SemaphoreRunnable implements Runnable{
        Semaphore semaphore;

        public SemaphoreRunnable(Semaphore semaphore) {
            this.semaphore = semaphore;
        }

        @Override
        public void run() {
            try {
                semaphore.acquire();
                System.out.println( "critical section: " + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
                Thread.sleep(1000);
                semaphore.release();

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

output:

```
critical section: 2017-06-13 14:57:58
critical section: 2017-06-13 14:57:58
critical section: 2017-06-13 14:57:59
critical section: 2017-06-13 14:57:59
critical section: 2017-06-13 14:58:00
```



### Sending Signals Between Threads

If you use a semaphore to send signals between threads, then you would typically have one thread call the `acquire()` method, and the other thread to call the `release()` method.

If no permits are available, the `acquire()` call will block until a permit is released by another thread. Similarly, a `release()` calls is blocked if no more permits can be released    into this semaphore.

Thus it is possible to coordinate threads. For instance, if acquire was called after Thread 1 had inserted an object in a shared list, and Thread 2 had called `release()` just before taking an object from that list, you had essentially created a blocking queue. The number of permits available in the semaphore would correspond to the maximum number of elements the blocking queue could hold.



## Fairness

No guarantees are made about [fairness](http://tutorials.jenkov.com/java-concurrency/starvation-and-fairness.html) of the threads acquiring permits from the `Semaphore`. That is, there is no guarantee that the first    thread to call `acquire()` is also the first thread to obtain a permit. If the first thread is blocked waiting for a permit, then a second thread checking for a permit just as a permit is released, may actually obtain the permit ahead of thread 1.

If you want to enforce fairness, the `Semaphore` class has a constructor that takes a boolean telling if the semaphore should enforce fairness. Enforcing fairness comes at a performance / concurrency penalty, so don't enable it unless you need it.

Here is how to create a `Semaphore` in fair mode:

```java
Semaphore semaphore = new Semaphore(1, true);
```



# References

[Semaphore](http://tutorials.jenkov.com/java-util-concurrent/semaphore.html)



---

created at 2017-06-13 14:37