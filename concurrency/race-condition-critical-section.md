# Concepts

A ***critical section*** is a section of code that is executed by multiple threads.

When the result of multiple threads executing a critical section may differ depending on the sequence in which the threads execute, the critical section is said to **contain a *race condition***. 

The situation where two threads compete for the same resource, where the sequence in which the resource is accessed is significant, is called **race conditions**.A code section that leads to race conditions is called a **critical section**.



# Preventing Race Conditions

Race conditions can be avoided by proper thread synchronization in critical sections. 



# Critical Section Throughput

For larger critical sections it may be beneficial to **break the critical section into smaller critical sections**, to allow multiple threads to execute each a smaller critical section. This may decrease contention on the shared resource,and thus increase throughput of the total critical section.

```java
public class TwoSums {
    
    private int sum1 = 0;
    private int sum2 = 0;

    private Integer sum1Lock = new Integer(1);
    private Integer sum2Lock = new Integer(2);

    public void add(int val1, int val2){
        synchronized(this.sum1Lock){
            this.sum1 += val1;   
        }
        synchronized(this.sum2Lock){
            this.sum2 += val2;
        }
    }
}
```



# References

[Race Conditions and Critical Sections](http://tutorials.jenkov.com/java-concurrency/race-conditions-and-critical-sections.html)

---

created at 2017-05-05 19:50