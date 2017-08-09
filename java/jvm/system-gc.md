# System.html#gc()

```java
public static void gc()
```

 Calling the `gc` method **suggests** that the Java Virtual Machine expend effort toward recycling unused objects in order to make the memory they currently occupy available for quick reuse. When control returns from the method call, the Java Virtual Machine has made a best effort to reclaim space from all discarded objects. 

 The call `System.gc()` is effectively equivalent to the call: 

> ```java
>  Runtime.getRuntime().gc() 
> ```

- See Also:

  [`Runtime.gc()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Runtime.html#gc%28%29)



# Runtime.html#gc()

```java
public void gc()
```

Runs the garbage collector. Calling this method **suggests** that the Java virtual machine expend effort toward recycling unused objects in order to make the memory they currently occupy available for quick reuse. When control returns from the method call, the virtual machine has made its best effort to recycle all discarded objects.

The name `gc` stands for "garbage collector". The virtual machine performs this recycling process automatically **as needed**, in a separate thread, even if the `gc` method is not invoked explicitly. 

The method [`System.gc()`](https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#gc%28%29) is the conventional and convenient means of invoking this method.





# References

[System.html#gc()](https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#gc())

[Runtime.html#gc()](https://docs.oracle.com/javase/7/docs/api/java/lang/Runtime.html#gc())

[When does System.gc() do anything](https://stackoverflow.com/questions/66540/when-does-system-gc-do-anything)

---

created at 2017-08-09 22:35