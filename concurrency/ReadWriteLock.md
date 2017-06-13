# Lock

A `java.util.concurrent.locks.Lock` is a thread synchronization mechanism just like `synchronized` blocks. A `Lock` is, however, more flexible and more sophisticated than a synchronized block.

A `java.util.concurrent.locks.Lock` is a thread synchronization mechanism just like `synchronized` blocks. A `Lock` is, however, more flexible and more sophisticated than a synchronized block.

## Main Differences Between Locks and Synchronized Blocks

The main differences between a `Lock` and a synchronized block are:

- A synchronized block makes no guarantees about the sequence in which threads waiting to entering it are granted access.

- You cannot pass any parameters to the entry of a synchronized block. Thus, having a timeout trying to get access to a synchronized block is not possible.

- The synchronized block must be fully contained within a single method. A `Lock` can have it's calls to `lock()` and `unlock()`  in separate methods.  

    

# ReadWriteLock

A `java.util.concurrent.locks.ReadWriteLock` is an advanced thread lock mechanism. It allows multiple threads to read a certain resource, but only one to write it, at a time.

The idea is, that multiple threads can read from a shared resource without causing concurrency errors. The concurrency errors first occur when reads and writes to a shared resource occur concurrently, or if multiple writes take place concurrently.

## ReadWriteLock Locking Rules

| **Read Lock   **                         | Write Lock                               |
| ---------------------------------------- | ---------------------------------------- |
| If no threads have locked the `ReadWriteLock` for writing, and no thread have requested a write lock (but not yet obtained it). Thus, multiple threads can lock the lock for reading. | If no threads are reading or writing. Thus, only one thread at a time can lock the lock for writing. |



## ReadWriteLock Implementations

The `java.util.concurrent.locks` package contains the following `ReadWriteLock` implementation:

- ReentrantReadWriteLock



## Practice

```java
/**
 * Created by WS on 2017/6/13.
 */
public class ReadWriteLockTest {

    @Test
    public void test() throws InterruptedException {
        ReadWriteLock readWriteLock  = new ReentrantReadWriteLock();
        StringBuilder sb = new StringBuilder("append:");

        int i = 14;
        while ( i > 0 ){
            if( i%3 == 0 ){
                new Thread(new WriteRunnable(readWriteLock,sb)).start();
            }else {
                new Thread(new ReadRunnable(readWriteLock,sb)).start();
            }
            i--;
        }

        Thread.sleep(5000);
    }

    class ReadRunnable implements Runnable{
        ReadWriteLock readWriteLock ;
        StringBuilder sb;

        public ReadRunnable(ReadWriteLock readWriteLock, StringBuilder sb) {
            this.readWriteLock = readWriteLock;
            this.sb = sb;
        }

        @Override
        public void run() {
            this.readWriteLock.readLock().lock();
            try {
                String threadName = Thread.currentThread().getName();
                System.out.println( threadName + " read the value " + this.sb.toString() + " in read lock. " );
            }finally {
                this.readWriteLock.readLock().unlock();
            }
        }
    }

    class WriteRunnable implements Runnable{

        ReadWriteLock readWriteLock ;
        StringBuilder sb;

        public WriteRunnable(ReadWriteLock readWriteLock, StringBuilder sb) {
            this.readWriteLock = readWriteLock;
            this.sb = sb;
        }

        @Override
        public void run() {

            this.readWriteLock.writeLock().lock();
            try {
                String threadName = Thread.currentThread().getName();
                int random = new Random().nextInt(10);
                sb.append(random);
                System.out.println(threadName + " [[[write]]] the value " + sb.toString() + " in write lock.");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                this.readWriteLock.writeLock().unlock();
            }
        }
    }
}
```

output:

```
Thread-0 read the value append: in read lock. 
Thread-1 read the value append: in read lock. 
Thread-2 [[[write]]] the value append:8 in write lock.
Thread-4 read the value append:8 in read lock. 
Thread-5 [[[write]]] the value append:80 in write lock.
Thread-6 read the value append:80 in read lock. 
Thread-7 read the value append:80 in read lock. 
Thread-8 [[[write]]] the value append:802 in write lock.
Thread-9 read the value append:802 in read lock. 
Thread-10 read the value append:802 in read lock. 
Thread-11 [[[write]]] the value append:8027 in write lock.
Thread-12 read the value append:8027 in read lock. 
Thread-3 read the value append:8027 in read lock. 
Thread-13 read the value append:8027 in read lock. 
```





# References

[Lock](http://tutorials.jenkov.com/java-util-concurrent/lock.html)

[ReadWriteLock](http://tutorials.jenkov.com/java-util-concurrent/readwritelock.html)



---

created at 2017-06-13 15:31