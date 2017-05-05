# Atomicity

In ancient philosophy, an atom was the ultimate unit of matter on which more complex views of material reality were based. In computer programming, atomic describes a unitary action or object that is essentially indivisible, unchangeable, whole, and irreducible. 



- An operation acting on shared memory is atomic if it completes in a single step relative to other threads. 
- When an atomic store is performed on a shared memory, no other thread can observe the modification half-complete. 
- When an atomic load is performed on a shared variable, it reads the entire value as it appeared at a single moment in time.



# In java

In programming, an *atomic* action is one that effectively happens all at once. An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all. No side effects of an atomic action are visible until the action is complete.

- Reads and writes are atomic for reference variables and for most primitive variables (all types except `long` and `double`).
- Reads and writes are atomic for *all* variables declared **`volatile`** (*including* `long` and `double` variables).

### Example

Suppose `foo` is a variable of type `long`. The following operation is not an atomic operation:

```java
foo = 65465498L;
```

Indeed, the variable is written using two separate operations: one that writes the first 32 bits, and a second one which writes the last 32 bits. That means that another thread might read the value of `foo`, and **see the intermediate state**. 

> So that is assuming that it is running in a 32-bit system. if it was 64 bit system,Will foo = 65465498L; be atomic.

**Make the Operation atomic:**

```java
private volatile long foo;
```

or

```java
public synchronized void setFoo(long value) {
    this.foo = value;
}

public synchronized long getFoo() {
    return this.foo;
}
// no other use of foo outside of these two methods, unless also synchronized
```

or

```java
private AtomicLong foo;
```





# References

[Atomic Access](https://docs.oracle.com/javase/tutorial/essential/concurrency/atomic.html)

[What does “atomic” mean in programming?](http://stackoverflow.com/questions/15054086/what-does-atomic-mean-in-programming)



---

created at 2017-05-05 20:54