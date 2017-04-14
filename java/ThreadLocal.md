# Overview

The `ThreadLocal` class in Java enables you to create variables that can only be read and written by the same thread. Thus, even if two threads are executing the same code, and the code has a reference to a  `ThreadLocal` variable, then the two threads cannot see each other's `ThreadLocal` variables.

# Using

#### Initial ThreadLocal Value

```java
private ThreadLocal myThreadLocal = new ThreadLocal<String>() {
    @Override protected String initialValue() {
        return "This is the initial value";
    }
};  
```

Now all threads will see the same initial value when calling `get()` before having called `set()` .

  Normally, this method is invoked at most once per thread, but it may be invoked again in case of
 subsequent invocations of [`remove()`](https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html#remove%28%29) followed by [`get()`](https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html#get%28%29).

#### Full ThreadLocal Example

```java
public class ThreadLocalExample {

    public static class MyRunnable implements Runnable {
        private ThreadLocal<Integer> threadLocal = new ThreadLocal<Integer>();

        @Override
        public void run() {
            threadLocal.set( (int) (Math.random() * 100D) );
    
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
            }
    
            System.out.println(threadLocal.get());
        }
    }

    public static void main(String[] args) {
        MyRunnable sharedRunnableInstance = new MyRunnable();

        Thread thread1 = new Thread(sharedRunnableInstance);
        Thread thread2 = new Thread(sharedRunnableInstance);

        thread1.start();
        thread2.start();

        thread1.join(); //wait for thread 1 to terminate
        thread2.join(); //wait for thread 2 to terminate
    }
}
```

# GC

Each thread holds an implicit reference to its copy of a thread-local variable as long as the thread is alive and the `ThreadLocal` instance is accessible; after a thread goes away, all of its copies of thread-local instances are subject to garbage collection (unless other references to these copies exist).

## InheritableThreadLocal

​    The `InheritableThreadLocal` class is a subclass of `ThreadLocal`. Instead of each thread    having its own value inside a `ThreadLocal`, the `InheritableThreadLocal` grants access    to values to a thread and all child threads created by that thread.



# SourceCode

> **java.lang.ThreadLocal#get**

```java
/**
  * Returns the value in the current thread's copy of this
  * thread-local variable.  If the variable has no value for the
  * current thread, it is first initialized to the value returned
  * by an invocation of the {@link #initialValue} method.
  *
  * @return the current thread's value of this thread-local
*/
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```

> **java.lang.ThreadLocal#set**

```java
/**
  * Sets the current thread's copy of this thread-local variable
  * to the specified value.  Most subclasses will have no need to
  * override this method, relying solely on the {@link #initialValue}
  * method to set the values of thread-locals.
  *
  * @param value the value to be stored in the current thread's copy of
  *        this thread-local.
*/
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```

> **java.lang.ThreadLocal#getMap**

```java
/**
 * Get the map associated with a ThreadLocal. Overridden in
 * InheritableThreadLocal.
 *
 * @param  t the current thread
 * @return the map
 */
 ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;/* ThreadLocal values pertaining to this thread. This map is maintained by the ThreadLocal class.*/ 
}
```

# References

[Java ThreadLocal](http://tutorials.jenkov.com/java-concurrency/threadlocal.html)

[Class ThreadLocal<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html)



---

created at 2017-04-14 21:35

