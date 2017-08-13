# Garbage Collectors

## Common Heap Related Switches

| **Switch**      | **Description**                          |
| --------------- | ---------------------------------------- |
| `-Xms`          | Sets the initial heap size for when the JVM starts. |
| `-Xmx`          | Sets the maximum heap size.              |
| `-Xmn`          | Sets the size of the Young Generation.   |
| -XX:PermSize    | Sets the starting size of the Permanent Generation. |
| -XX:MaxPermSize | Sets the maximum size of the Permanent Generation |



## The Serial GC

The serial collector is the default for client style machines in Java SE 5 and 6. With the serial collector, both minor and major garbage collections are done serially (using a single virtual CPU). In addition, it uses a mark-compact collection method. This method moves older memory to the beginning of the heap so that new memory allocations are made into a single continuous chunk of memory at the end of the heap. This compacting of memory makes it faster to allocate new chunks of memory to the heap.

#### Usage Cases

The Serial GC is the garbage collector of choice for most applications that do not have low pause time requirements and run on **client-style machines**. It takes advantage of only a single virtual processor for garbage collection work (therefore, its name). Still, on today's hardware, the Serial GC can efficiently manage a lot of non-trivial applications with a few hundred MBs of Java heap, with relatively short worst-case pauses (around a couple of seconds for full garbage collections).

Another popular use for the Serial GC is in environments where **a high number of JVMs are run on the same machine (in some cases, more JVMs than available processors!)**. In such environments when a JVM does a garbage collection it is better to use only one processor to minimize the interference on the remaining JVMs, even if the garbage collection might last longer. And the Serial GC fits this trade-off nicely.

Finally, with the proliferation of **embedded hardware** with minimal memory and few cores, the Serial GC could make a comeback.

#### Command Line Switches

To enable the Serial Collector use:
` -XX:+UseSerialGC`

Here is a sample command line for starting the `Java2Demo`:
 `java -Xmx12m -Xms3m -Xmn1m -XX:PermSize=20m -XX:MaxPermSize=20m -XX:+UseSerialGC -jar c:\javademos\demo\jfc\Java2D\Java2demo.jar`























# References

[Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)

---

created at 2017-08-14 00:11