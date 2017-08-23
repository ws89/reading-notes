# Throughput Collector

For most application areas that we find in practice, a garbage collection (GC) algorithm is being evaluated according to two criteria:

1. The higher the achieved throughput, the better the algorithm.
2. The smaller the resulting pause times, the better the algorithm.



## throughput

The JVM always executes a GC in dedicated threads, the “GC threads”. Whenever GC threads are active, they compete against the actual “application threads” for available processors and thus CPU time. Simplified just a little bit, we refer by “throughput” to the fraction of total program execution time at which the application threads are running. For example, a throughput of 99/100 means that on average the application threads are running 99 out of 100 seconds of program execution time, while the GC threads are only running for one second during the same time span.



## pause time

The term “pause time” refers to a time span in which the **application threads are paused completely** in favor of the GC threads. For example, a pause time of 100 milliseconds during a GC means that no application thread was active during that 100 millisecond interval. If we then refer to an “average pause time” of 100 milliseconds for a running application, we state that all pause times observed for that application had an average length of 100 milliseconds. Similarly, a “maximum pause time” of 100 milliseconds means that none of the pause times observed were larger than 100 milliseconds.



## Throughput vs. pause times

A high throughput is desirable because only the application threads perform “productive” work in the perception of the end user of the application. Intuitively, an application runs faster if the throughput is higher. Low pause times are desirable as well, because from the end user’s perspective a hanging application is always undesirable regardless of whether the stalls are being caused by GC or other reasons. Depending on the type of application, even short pauses of 200 milliseconds can disrupt end user experience. Thus, it is important to have a low maximum pause time, in particular for an interactive application.

Unfortunately, “high throughput” and “low pause times” are competing goals. Think about it this way, again a bit simplified for the sake of clarity: A GC requires certain preconditions in order to run safely. For example, it must be guaranteed that application threads do not modify the state of objects while the GC threads try to decide which objects are still referenced and which ones are not. For this reason, the application threads must be stopped during a GC (or, depending on the algorithm used, only during certain phases of a GC). This, however, **causes additional costs for thread scheduling: direct costs through context switches and indirect costs because of cache effects.** Together with the costs for additional JVM-internal safety measures, this means that each GC comes along with some non-negligible overhead, which adds up with the time taken by the GC threads to perform their actual work. Therefore, we can maximize throughput by running the GC as seldom as possible, i.e., only when it is unavoidable, to save all the overhead associated with it.

However, executing the GC only infrequently means that whenever a GC is run it has much more work to do, as the number of objects that accumulated on the heap in the meantime is much higher. A single GC takes more time until completion, which in turn causes higher average and maximum pause times. Thus, with low pause times in mind, it would be desirable to run the GC more frequently so that each single run completes more quickly. This in turn adds overhead and causes throughput to decline, and we are back where we started.

In summary, when designing (or using!) a GC algorithm we have to decide what we are aiming for: A GC algorithm may **target one of the two goals only** (i.e., solely focus on maximum throughput or on minimal pause times) or try to **find a compromise between them**.



## Garbage collection on the HotSpot JVM

For the old generation, the HotSpot JVM mainly offers two classes of GC algorithms (leaving aside the new G1 GC algorithm for now). The first class contains algorithms that try to **maximize throughput** while the second class tries to **minimize pause times**. 

As we want to focus on configuration flags, I will only give a brief overview of the throughput-oriented GC algorithms offered by HotSpot. The GC algorithm is triggered when an object allocation in the old generation fails due to lack of space (usually, the “allocation” is in fact a promotion of an object from the young generation). Starting at so-called “GC roots”, the GC then searches the heap for reachable objects and marks them as alive. Afterwards, the GC moves the live objects within the old generation so that they occupy a single, non-fragmented memory block, and takes a note that the remaining memory area is free. That is, we don’t follow a copying strategy into a different heap area, like the young generation GC algorithm does. Instead, we keep all objects in the same heap area, thereby defragmenting that area. The collectors use one or more threads to perform GC. When more than one thread is used, the different steps of the algorithm are subdivided such that each GC thread mostly works in its own area without interfering with others. During a GC, all application threads are paused, getting restarted only when the GC is finished. Now let us take a look at the most important flags regarding the **throughput-oriented** GC algorithms.



### -XX:+UseSerialGC

We use this flag to activate the serial, i.e., **single-threaded** version of the throughput-oriented garbage collector. **Both the young generation and the old generation GC will be executed by a single GC thread only.** This flag is to be recommended for JVMs that **only have a single processor core available**. In such a situation, using multiple GC threads would even be counterproductive because these threads would compete for CPU resources and cause synchronization overhead but never actually run in parallel.



### -XX:+UseParallelGC

With this flag, we tell the JVM to execute the young generation GC in parallel using multiple GC threads. With Java 6, in my opinion, this flag should not be used because `-XX:+UseParallelOldGC` is clearly preferable. Note that with Java 7 the situation has changed a bit (see, e.g., [this overview](http://www.fasterj.com/articles/oraclecollectors1.shtml)) so that `-XX:+UseParallelGC` may be used to the same effect as `-XX:+UseParallelOldGC`.



### -XX:+UseParallelOldGC

The naming of this flag is a little unfortunate because “old” sounds like “deprecated”. However, “old” actually refers to the old generation, which explains why `-XX:+UseParallelOldGC` is preferable over `-XX:+UseParallelGC`: in addition to a parallel young generation GC, it also activates parallel old generation GC. I recommend using this flag whenever high throughput it desired and the JVM has two or more processor cores available.

As a sidenote, the parallel versions of the throughput-oriented HotSpot GC algorithms are often called “throughput collectors” (or also: “the Throughput Collector”) because they aim at increased throughput through parallel execution.



### -XX:ParallelGCThreads

With `-XX:ParallelGCThreads=<value>` we can specify the number of GC threads to use for parallel GC. For example, with `-XX:ParallelGCThreads=6` each parallel GC will be executed with six threads. If we don’t explicitly set this flag, the JVM will use a default value which is computed based on the number of available (virtual) processors. The determining factor is the value `N` returned by the Java method `Runtime.availableProcessors()`. For `N <= 8` parallel GC will use just as many, i.e., `N` GC threads. For `N > 8` available processors, the number of GC threads will be computed as `3+5N/8`.

Using the default setting makes most sense when the JVM uses the system and its processors exclusively. However, if more than one JVM (or other CPU-hungry systems) are all running on the same machine, we should use `-XX:ParallelGCThreads` in order to reduce the number of GC threads to an adequate value. For example, if four server JVMs are running on a machine with 16 processor cores, then `-XX:ParallelGCThreads=4` is a sensible choice so that GCs of different JVMs don’t interfere with each other.



### -XX:-UseAdaptiveSizePolicy

The throughput collectors offer an interesting (but common, at least on modern JVMs) mechanism to improve user-friendliness of GC configuration. This mechanism is part of what is known as “ergonomics”, a concept introduced for HotSpot with Java 5. With ergonomics, the garbage collector may dynamically apply modifications to the different heap areas sizes as well as the GC settings if it has evidence that these modifications would improve GC performance. The precise meaning of “improve GC performance” may be specified by the user via the flags `-XX:GCTimeRatio` and `-XX:MaxGCPauseMillis` (see below).

**It is important to know that ergonomics is activated by default** – which is fine, as adaptive behavior is one of the biggest strengths of the JVM. Still, sometimes we may have a pretty clear idea of what settings are best for a particular application, and in these cases we might not want the JVM to mess around with our settings. Whenever we find ourselves in such a situation, we may consider deactivating some of the ergonomics by setting `-XX:-UseAdaptiveSizePolicy`.



#### -XX:GCTimeRatio

With `-XX:GCTimeRatio=<value>` we tell the JVM a target value for the throughput to achieve. More precisely, a value of `-XX:GCTimeRatio=N` specifies a target fraction of `N/(N+1)` for the execution time of the application threads (related to the total program execution time). For example, with `-XX:GCTimeRatio=9` we demand that the application threads should be active for at least 9/10 of the total execution time (and, accordingly, the GC threads for the remaining 1/10). Based on measurements at run time, the JVM will then try to modify the heap and GC configuration such that the the target throughput is reached. The default value for `-XX:GCTimeRatio` is 99, i.e., the application threads should run for at least 99 percent of the total execution time.



#### -XX:MaxGCPauseMillis

The flag `-XX:MaxGCPauseMillis=<value>` tells the JVM a target value (in milliseconds) for the maximum pause time. At run time, the throughput collector then computes statistics (a weighted average and the standard deviation) over the pause times it observes. If the statistics suggest that there is a risk of experiencing pause times exceeding the target value, the JVM modifies heap and GC settings in order to reduce them. Note that the statistics are computed separately for the young and old generation GCs. Also note that, **by default, there is no target value set for the maximum pause time**.

If both target values for the maximum pause time and the minimum throughput are set, **achieving the maximum pause time goal has higher priority**. Of course, there is no guarantee at all that the JVM will achieve either goal, even though it will try hard to do so. In the end, everything depends on the behavior of the application at hand.

When setting a maximum pause time target, we should take care not to choose too small a value. As we know by now, in order to keep pause times low, the JVM has to increase the total number of GCs which may 
seriously impact the achievable throughput. That’s why for applications that require low pause times as their primary goal (which is the case for most web applications), I would recommend to not use the throughput collector at all, but instead switch to the CMS collector. The CMS collector will be the topic of the next part of this series.





# References

[Useful JVM Flags – Part 6 (Throughput Collector)](https://blog.codecentric.de/en/2013/01/useful-jvm-flags-part-6-throughput-collector/)

[Oracle JVM Garbage Collectors Available From JDK 1.7.0_04 And After](http://www.fasterj.com/articles/oraclecollectors1.shtml)

---

created at 2017-08-23 21:56