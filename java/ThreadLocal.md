# Overview

The `ThreadLocal` class in Java enables you to create variables that can only be read and written by the same thread. Thus, even if two threads are executing the same code, and the code has a reference to a  `ThreadLocal` variable, then the two threads cannot see each other's `ThreadLocal` variables.

# Usage

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

> **java.lang.ThreadLocal.ThreadLocalMap#expungeStaleEntry**

```java
/**
 * Expunge a stale entry by rehashing any possibly colliding entries
 * lying between staleSlot and the next null slot.  This also expunges
 * any other stale entries encountered before the trailing null.  See
 * Knuth, Section 6.4
 *
 * @param staleSlot index of slot known to have null key
 * @return the index of the next null slot after staleSlot
 * (all between staleSlot and this slot will have been checked
 * for expunging).
 */
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    // expunge entry at staleSlot
    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    // Rehash until we encounter null
    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;

                // Unlike Knuth 6.4 Algorithm R, we must scan until
                // null because multiple entries could have been stale.
                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```



# InheritableThreadLocal

The `InheritableThreadLocal` class is a subclass of `ThreadLocal`. Instead of each thread    having its own value inside a `ThreadLocal`, the `InheritableThreadLocal` grants access to values to a thread and all child threads created by that thread.

## Usage

```java
@Test
public void threadLocalTest() throws InterruptedException {

    Thread parentThread = Thread.currentThread();
    parentThread.setName( "[ParentThread]" );

    ThreadLocal<String> parentThreadLocal = new ThreadLocal();
    parentThreadLocal.set( "[Parent-Thread-Local-Content]" );

    InheritableThreadLocal<String> parentInheritableThreadLocal = new InheritableThreadLocal();
    // or ThreadLocal<String> parentInheritableThreadLocal = new InheritableThreadLocal();
    parentInheritableThreadLocal.set( "[Parent-Inheritable-Thread-Local-Content]" );

    System.out.println( Thread.currentThread().getName() + "parentThreadLocalContent ->" + parentThreadLocal.get() );
    System.out.println( Thread.currentThread().getName() + "parentInheritableThreadLocalContent" + parentInheritableThreadLocal.get() );

    Runnable runnable = new Runnable() {
        @Override
        public void run() {
            System.out.println( Thread.currentThread().getName()
                    + "parentThreadLocalContent from child-thread ->" + parentThreadLocal.get() );

            System.out.println( Thread.currentThread().getName()
                    + "parentInheritableThreadLocalContent from child-thread ->" + parentInheritableThreadLocal.get() );
        }
    };
    Thread childThread1 = new Thread(runnable,"[Child-Thread-1]");
    Thread childThread2 = new Thread(runnable,"[Child-Thread-2]");

    childThread1.start();
    childThread2.start();

    childThread1.join();
    childThread2.join();
}
/*
output:
[ParentThread]parentThreadLocalContent ->[Parent-Thread-Local-Content]
[ParentThread]parentInheritableThreadLocalContent[Parent-Inheritable-Thread-Local-Content]
[Child-Thread-1]parentThreadLocalContent from child-thread ->null
[Child-Thread-2]parentThreadLocalContent from child-thread ->null
[Child-Thread-2]parentInheritableThreadLocalContent from child-thread ->[Parent-Inheritable-Thread-Local-Content]
[Child-Thread-1]parentInheritableThreadLocalContent from child-thread ->[Parent-Inheritable-Thread-Local-Content]
*/
```



# References

[Java ThreadLocal](http://tutorials.jenkov.com/java-concurrency/threadlocal.html)

[Class ThreadLocal<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html)

[Java Secret: InheritableThreadLocal](http://vanillajava.blogspot.com/2011/06/jaca-secret-inheritablethreadlocal.html)



---

created at 2017-04-14 21:35

updated at 2017-05-26 21:40