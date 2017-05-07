# Overview

All synchronized blocks synchronized on the same object can only have one thread executing inside them at the same time.

The `synchronized` keyword can be used to mark four different types    of blocks:

1. Instance methods
2. Static methods
3. Code blocks inside instance methods
4. Code blocks inside static methods




> **Synchronizing** all accesses to a variable allows only a single thread at a time to access the variable, and forces all other threads to wait for that accessing thread to release its access to the variable. 



# Usage

### synchronized( lockObject ){ /** perform code */}

>  wrong

``` java
@Test
public void testSynchronized(){
  Object lockObject = null; 
  synchronized(lockObject){ // lockObject is null, JVM will throw NullPointerException
    System.out.println("execution");
  }
}
```

- **lockObject can't be null**.

> right

``` java
@Test
public void testSynchronized(){
  Object lockObject = new Object();
  synchronized(lockObject){
    System.out.println("execution");
  }
}
```



# References

- [Synchronized Methods](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html)

- [Java Synchronized Blocks](http://tutorials.jenkov.com/java-concurrency/synchronized.html#synchronized-keyword)

- [What is the difference between atomic / volatile / synchronized?](http://stackoverflow.com/questions/9749746/what-is-the-difference-between-atomic-volatile-synchronized)

------

created at 2017-03-29 21:15

updated at 2017-05-05 19:46