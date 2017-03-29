# Using

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

------

created at 2017-03-29 21:15