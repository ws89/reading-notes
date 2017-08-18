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








# References

[Garbage Collection in Java](http://java-latte.blogspot.in/2013/08/garbage-collection-in-java.html)

[Runtime Data Areas – Java’s Memory Model](http://www.pointsoftware.ch/en/under-the-hood-runtime-data-areas-javas-memory-model/)



---

created at 2017-08-16 23:17