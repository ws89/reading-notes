# CountDownLatch

A `java.util.concurrent.CountDownLatch` is a concurrency construct that allows one or more threads to wait for a given set of operations to complete.

A `CountDownLatch` is initialized with a given count. This count is decremented by calls to the  `countDown()` method.Calling `await()` blocks the thread until the count reaches zero.

Below is a simple example. After the `Decrementer` has called `countDown()` 2 times on the  `CountDownLatch`, the waiting `Waiter` is released from the `await()` call.

### Practice

```java
public class CountDownLatchTest {

    @Test
    public void test() throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(2);

        new Thread(new Waiter(countDownLatch)).start();
        new Thread(new Decrementer(countDownLatch)).start();

        Thread.sleep(2000);
    }

    class Waiter implements Runnable{

        private CountDownLatch countDownLatch;

        public Waiter(CountDownLatch countDownLatch) {
            this.countDownLatch = countDownLatch;
        }

        @Override
        public void run() {
            try {
                System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));//2017-06-09 12:42:01
                this.countDownLatch.await();
                System.out.println(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));//2017-06-09 12:42:03
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Waiter released.");
        }
    }

    class Decrementer implements Runnable{

        private CountDownLatch countDownLatch;

        public Decrementer(CountDownLatch countDownLatch) {
            this.countDownLatch = countDownLatch;
        }

        @Override
        public void run() {
            try {
                Thread.sleep(1000);
                this.countDownLatch.countDown();

                Thread.sleep(1000);
                this.countDownLatch.countDown();
            }catch (InterruptedException e){
                e.printStackTrace();
            }

        }
    }
}
```



# References

[CountDownLatch](http://tutorials.jenkov.com/java-util-concurrent/countdownlatch.html)



---

created at 2017-06-09 12:25