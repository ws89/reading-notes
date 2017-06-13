# CyclicBarrier

The `java.util.concurrent.CyclicBarrier` class is a synchronization mechanism that can synchronize threads progressing through some algorithm. In other words, it is a barrier that all threads must wait at, until all threads reach it, before any of the threads can continue.

![Two threads waiting for each other at CyclicBarriers.](http://tutorials.jenkov.com/images/java-concurrency-utils/cyclic-barrier.png)

The threads wait for each other by calling the `await()` method on the `CyclicBarrier`. Once N threads are waiting at the `CyclicBarrier`, all threads are released and can continue running.



The waiting threads waits at the `CyclicBarrier` until either:

- The last thread arrives (calls await() )
- The thread is interrupted by another thread (another thread calls its interrupt() method)
- Another waiting thread is interrupted
- Another waiting thread times out while waiting at the `CyclicBarrier`
- The `CyclicBarrier.reset()` method is called by some external thread.



## Practice

```java
/**
 * Created by WS on 2017/6/13.
 */
public class CyclicBarrierTest {

    @Test
    public void test() throws BrokenBarrierException, InterruptedException {

        Runnable barrierAction1 = new Runnable() {
            @Override
            public void run() {
                System.out.println( "BarrierAction1 executed." );
            }
        };

        Runnable barrierAction2 = new Runnable() {
            @Override
            public void run() {
                System.out.println( "BarrierAction2 executed." );
            }
        };


        CyclicBarrier cyclicBarrier1 = new CyclicBarrier(2,barrierAction1);
        CyclicBarrier cyclicBarrier2 = new CyclicBarrier(2,barrierAction2);

        CyclicBarrierRunnable cyclicBarrierRunnable1 = new CyclicBarrierRunnable(cyclicBarrier1,cyclicBarrier2);
        CyclicBarrierRunnable cyclicBarrierRunnable2 = new CyclicBarrierRunnable(cyclicBarrier1,cyclicBarrier2);

        new Thread(cyclicBarrierRunnable1).start();
        new Thread(cyclicBarrierRunnable2).start();

        Thread.sleep(4000);
    }

    class CyclicBarrierRunnable implements Runnable{

        CyclicBarrier cyclicBarrier1 ;
        CyclicBarrier cyclicBarrier2 ;

        public CyclicBarrierRunnable(CyclicBarrier cyclicBarrier1, CyclicBarrier cyclicBarrier2) {
            this.cyclicBarrier1 = cyclicBarrier1;
            this.cyclicBarrier2 = cyclicBarrier2;
        }

        @Override
        public void run() {
            try {
                String threadName = Thread.currentThread().getName();

                Thread.sleep(1000);
                System.out.println( threadName + " waiting at barrier 1 " );
                this.cyclicBarrier1.await();

                Thread.sleep(1000);
                System.out.println( threadName + " waiting at barrier 2 " );
                this.cyclicBarrier2.await();

                System.out.println( threadName + " done " );

            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
    }

}
```

output:

```
Thread-0 waiting at barrier 1 
Thread-1 waiting at barrier 1 
BarrierAction1 executed.
Thread-0 waiting at barrier 2 
Thread-1 waiting at barrier 2 
BarrierAction2 executed.
Thread-1 done 
Thread-0 done 
```



# References

[CyclicBarrier](http://tutorials.jenkov.com/java-util-concurrent/cyclicbarrier.html)



---

created at 2017-06-13 13:23