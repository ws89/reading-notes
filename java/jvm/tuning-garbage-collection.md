# Terms used in Tuning Garbage Collection

## Perm area size

You can set the Perm area size with the `-XX:PermSize` and `-XX:MaxPermSize` options but only when OutOfMemoryError occurs and the cause is the Perm area size.



## Heap area size

- `-Xms`   Heap area size when starting JVM
- `-Xmx`  Maximum heap area size



## New area size

- `-XX:NewRatio`    Ratio of New area and Old area
- `-XX:NewSize`      New area size
- `-XX:SurvivorRatio`    Ratio of Eden area and Survivor area



## Sizing the Generations

- The `-Xmx` value determines the size of the heap to reserve at JVM initialization
- The `-Xms` value is the space in memory that is committed to the VM at init. The JVM can grow to the size of `-Xmx`
- The difference between `-Xmx` and`-Xms` is virtual memory (virtually committed)



## Total Heap

- Total available memory is the most important factor affecting GC performance
- By default the JVM grows or shrinks the heap at each GC to keep the ratio of free space to live objects at each collection within a specified range.
  `-XX:MinHeapFreeRatio` \- when the percentage of free space in a generation falls below this value the generation will be expanded to meet this percentage. Default is 40
  `-XX:MaxHeapFreeRatio` - when the percentage of free space in a generation exceeded this value the 
  generation will shrink to meet this value. Default is 70



## The Young Generation

The bigger the young generation the less minor GC's, but this implies a smaller tenured generation which increases the frequency of major collections.



### Young Generation Guarantee

- The `-XX:SurvivorRatio` option can be used to tune the number of survivor spaces
- Not often important for performance



#### -XX:SurvivorRatio

It is "the ratio between each survivor space and eden space" . The SurvivorRatio parameter controls the size of the two survivor spaces.

**For example**, `-XX:SurvivorRatio=6` sets the ratio between each survivor space and eden to be 1:6, each survivor space will be one eighth of the young generation.

**To clear this**, consider x= s0 (survior one), x= s2 (survior two)  and 6x = eden space means total= 8x for young Generation. It's clear that each survivor space will be one eighth of the young generation.



#### -XX:NewSize

It is sum of Survivor Space (both S0 and S1) and Eden Space



#### -XX:NewRatio

NewRatio is the ratio of the New area(sum of Survivor Space (both S0 and S1) and Eden Space.) and the Old area (Old Generation).

If `XX:NewRatio=1`, New area:Old area is 1:1. For 1 GB, New area:Old area is 500MB: 500MB. 

If NewRatio is 2, New area:Old area is 1:2. Therefore, as the value gets larger, the Old area size gets larger and the New area size gets smaller.Old Generation = 2/3 of the heap.



#### -XX:+PrintTenuringDistribution 

shows the threshold chosen by JVM to keep survivors half full, and the ages of objects in the new generation.



## Example

```
Xmx / Xms = 1000 MB
NewRatio = 3

From this we can calculate 
Old area (Old Generation) = 750 & New Area (the sum of Survivor Space (both S0 and S1) and Eden Space) = 250

If SurvivorRatio = 10 then each Survivor Space = 1/12 of Eden  i.e, 20.888 = 21
S0 & S1 = 21
Eden space= 208

```





# OutOfMemoryErrors 

- Exception in thread “main”: java.lang.OutOfMemoryError: **Java heap space**

  Reason: an object could not be allocated into the heap space.

- Exception in thread “main”: java.lang.OutOfMemoryError: **PermGen space**

  Reason: classes and methods could not be loaded into the PermGen space. 
  This occurs when an application requires a lot of classes e.g. in various 3rd party libraries.

- Exception in thread “main”: java.lang.OutOfMemoryError: **Requested array size exceeds VM limit**

  Reason: this occurs when an arrays is created larger than the heap size.

- Exception in thread “main”: java.lang.OutOfMemoryError: **request bytes for . Out of swap space?**

  Reason: this occurs when an allocation from the native heap failed and might be close to its limit. The indicates the source of the module where this error occurs.

- Exception in thread “main”: java.lang.OutOfMemoryError: **(Native method)**

  Reason: this error indicates that the problem originates from a native call rather than in the JVM.




# Young Generation

Most objects “die” young, i.e., after their creation they are not referenced for long in the program flow. Also, it has been observed that young objects are rarely referenced by older objects.

The Sun/Oracle HotSpot JVM further divides the young generation into three sub-areas: **one large** area named “Eden” and **two smaller** “survivor spaces” named “From” and “To”. As a rule, new objects are allocated in “Eden” (with the exception that **if a new object is too large to fit into “Eden” space, it will be** 
**directly allocated in the *old generation***).

Based on the assumption that most of the young objects may be deleted during a GC, a copying strategy (“copy collection”) is being used for young generation GC. At the beginning of a GC, the survivor space “To” is empty and objects can only exist in “Eden” or “From”. Then, during the GC, all objects in “Eden” that are still being referenced are moved into “To”. Regarding “From”, the still referenced objects in this space are handled depending on their age. If they have not reached a certain age (“tenuring threshold”), they are also moved into “To”. Otherwise they are moved into the old generation. At the end of this copying procedure, “Eden” and “From” can be considered empty (because they only contain dead objects), and all live objects in the young generation are located in “To”. Should “To” fill up at some point during the GC, all remaining objects are moved into the old generation instead (and will never return). As a final step, “From” and “To” swap their roles (or, more precisely, their names) so that “To” is empty again for the next GC and “From” contains all remaining young objects.

![https://blog.codecentric.de/files/2011/08/young_gc.png](https://blog.codecentric.de/files/2011/08/young_gc.png)

Example showing the initial state and the result of a young generation GC. Free space is green, objects not referenced anymore are yellow, and still referenced objects are red. In this example, the survivor spaces are large enough so that no objects need to be moved into the old generation.



It now becomes clear why young generation sizing is so important: ***If the young generation is too small, short-lived objects will quickly be moved into the old generation where they are harder to collect. Conversely, if the young generation is too large, we will have lots of unnecessary copying for long-lived objects that will later be moved to the old generation anyway.*** Thus we need to find a compromise somewhere between small and large young generation size. Unfortunately, finding the right compromise for a particular application can often only be done by systematic measurement and tuning. And that’s where the JVM flags come into play.



## -XX:NewSize and -XX:MaxNewSize

Similar to the total heap size (with `-Xms` and `-Xmx`) it is possible to explicitly set a lower and upper bound for the size of the young generation. However, when setting `-XX:MaxNewSize` we need to take into account that the young generation is only one part of the heap and that the larger we choose its size the smaller the old generation will be. For stability reasons it is not allowed to choose a young generation size larger than the old generation, because in the worst case it may become necessary for a GC to move all objects from the young generation into the old generation. Thus `-Xmx/2` is an upper bound for `-XX:MaxNewSize`.

For performance reasons we may also specify the initial size of the young generation using the flag `-XX:NewSize`. This is useful if we know the rate at which young objects are being allocated (for example because we measured it!) and can save some of the costs required for slowly growing the young generation to that size over time.



## -XX:NewRatio

It is also possible to specify the young generation size in relation to the size of the old generation. The potential advantage of this approach is that the young generation will grow and shrink automatically when the JVM dynamically adjusts the total heap size at run time. The flag `-XX:NewRatio` allows us to specify the factor by which the old generation should be larger than the young generation. For example, with `-XX:NewRatio=3` the old generation will be three times as large as the young generation. That is, the old generation will occupy 3/4 and the young generation will occupy 1/4 of the heap.

**If we mix absolute and relative sizing of the young generation, the absolute values always have precedence**. Consider the following example:

```java
$ java -XX:NewSize=32m -XX:MaxNewSize=512m -XX:NewRatio=3 MyApp
```

With these settings, the JVM will try to size the young generation at one third of the old generation size, but it will never let young generation size fall below 32 MB or exceed 512 MB.

There is no general rule if absolute or relative young generation sizing is preferable. If we know the memory usage of our application well, it can be advantageous to specify a fixed size both for the total heap and the young generation, and it can also be useful to specify a ratio. If we only know a little or maybe nothing at all about our application in this respect, the correct approach is to just let the JVM do the work and not to mess around with the flags. If the application runs smoothly, we can be happy that we didn’t put in extra effort where none was needed. And should we encounter performance problems or OutOfMemoryErrors, we would still need to first perform a series of meaningful measurements to narrow down the root cause of the problem before moving on to tuning.



## -XX:SurvivorRatio

The flag `-XX:SurvivorRatio` is similar to `-XX:NewRatio` but applies to the areas inside the young generation. The value of `-XX:SurvivorRatio` specifies how large “Eden” should be sized relative to one of the two survivor spaces. For example, with `-XX:SurvivorRatio=10` we dimension “Eden” ten times as large as “To” (and at the same time ten times as large as “From”). As a result, “Eden” occupies 10/12 of the young generation while “To” and “From” each occupy 1/12. Note that the two survivor spaces are always equal in size.

What effect does survivor space sizing have? Suppose that the survivor spaces are very small compared to “Eden”. Then we have lots of space in “Eden” for newly allocated objects, which is desirable. If all these objects can be collected during the next GC, “Eden” is empty again and everything is fine. However, if some of these young objects are still being referenced, we have only little space in the survivor spaces to accommodate them. As a consequence, most of these objects will be moved to the old generation right after their first GC, which is not desirable. Now let us consider the opposite situation: Suppose that the survivor spaces are relatively large in size. Then they have lots of space to fulfill their main purpose, to accommodate objects that survive one or more GCs but still die young. However, the smaller “Eden” space will be exhausted more quickly, which increases the number of young generation GCs performed. This is undesirable.

In summary, we want to minimize the number of short-lived objects that are prematurely moved into the old generation, but we also want to minimize the number and duration of young generation GCs. Once again we need to find a compromise, which in turn depends on the characteristics of the application at hand. A good starting point for finding an adequate compromise is to learn about the age distribution of the objects in the particular application.





# References

[Garbage Collection in Java](http://java-latte.blogspot.in/2013/08/garbage-collection-in-java.html)

[Runtime Data Areas – Java’s Memory Model](http://www.pointsoftware.ch/en/under-the-hood-runtime-data-areas-javas-memory-model/)

---

created at 2017-08-16 23:17