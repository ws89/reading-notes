# Exchanger

The `java.util.concurrent.Exchanger` class represents a kind of rendezvous point where two threads can exchange objects.

![Two threads exchanging objects via an Exchanger.](http://tutorials.jenkov.com/images/java-concurrency-utils/exchanger.png)

## Practice

```java
/**
 * Created by WS on 2017/6/13.
 */
public class ExchangerTest {

    @Test
    public void test() throws InterruptedException {
        Exchanger<String> exchanger = new Exchanger();

        new Thread(new ExchangerRunnable(exchanger,"A")).start();
        new Thread(new ExchangerRunnable(exchanger,"B")).start();

        Thread.sleep(2000);
    }

    class ExchangerRunnable implements Runnable{

        Exchanger<String> exchanger ;
        String string ;

        public ExchangerRunnable(Exchanger<String> exchanger, String string) {
            this.exchanger = exchanger;
            this.string = string;
        }

        @Override
        public void run() {
            try {
                String previous = this.string;
                String next = this.exchanger.exchange(this.string);

                System.out.println(Thread.currentThread().getName() + " exchanged " + previous + " for " + next);

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

output:

```
Thread-1 exchanged B for A
Thread-0 exchanged A for B
```



# References

[Exchanger](http://tutorials.jenkov.com/java-util-concurrent/exchanger.html)



---

created at 2017-06-13 14:16