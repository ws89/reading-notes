# Throughput Collector

For most application areas that we find in practice, a garbage collection (GC) algorithm is being evaluated according to two criteria:

1. The higher the achieved throughput, the better the algorithm.
2. The smaller the resulting pause times, the better the algorithm.



### throughput

The JVM always executes a GC in dedicated threads, the “GC threads”. Whenever GC threads are active, they compete against the actual “application threads” for available processors and thus CPU time. Simplified just a little bit, we refer by “throughput” to the fraction of total program execution time at which the application threads are running. For example, a throughput of 99/100 means that on average the application threads are running 99 out of 100 seconds of program execution time, while the GC threads are only running for one second during the same time span.



### pause time

The term “pause time” refers to a time span in which the **application threads are paused completely** in favor of the GC threads. For example, a pause time of 100 milliseconds during a GC means that no application thread was active during that 100 millisecond interval. If we then refer to an “average pause time” of 100 milliseconds for a running application, we state that all pause times observed for that application had an average length of 100 milliseconds. Similarly, a “maximum pause time” of 100 milliseconds means that none of the pause times observed were larger than 100 milliseconds.



### Throughput vs. pause times

A high throughput is desirable because only the application threads perform “productive” work in the perception of the end user of the application. Intuitively, an application runs faster if the throughput is higher. Low pause times are desirable as well, because from the end user’s perspective a hanging application is always undesirable regardless of whether the stalls are being caused by GC or other reasons. Depending on the type of application, even short pauses of 200 milliseconds can disrupt end user experience. Thus, it is important to have a low maximum pause time, in particular for an interactive application.

Unfortunately, “high throughput” and “low pause times” are competing goals. Think about it this way, again a bit simplified for the sake of clarity: A GC requires certain preconditions in order to run safely. For example, it must be guaranteed that application threads do not modify the state of objects while the GC threads try to decide which objects are still referenced and which ones are not. For this reason, the application threads must be stopped during a GC (or, depending on the algorithm used, only during certain phases of a GC). This, however, **causes additional costs for thread scheduling: direct costs through context switches and indirect costs because of cache effects.** Together with the costs for additional JVM-internal safety measures, this means that each GC comes along with some non-negligible overhead, which adds up with the time taken by the GC threads to perform their actual work. Therefore, we can maximize throughput by running the GC as seldom as possible, i.e., only when it is unavoidable, to save all the overhead associated with it.

However, executing the GC only infrequently means that whenever a GC is run it has much more work to do, as the number of objects that accumulated on the heap in the meantime is much higher. A single GC takes more time until completion, which in turn causes higher average and maximum pause times. Thus, with low pause times in mind, it would be desirable to run the GC more frequently so that each single run completes more quickly. This in turn adds overhead and causes throughput to decline, and we are back where we started.

In summary, when designing (or using!) a GC algorithm we have to decide what we are aiming for: A GC algorithm may **target one of the two goals only** (i.e., solely focus on maximum throughput or on minimal pause times) or try to **find a compromise between them**.



### Garbage collection on the HotSpot JVM

As we want to focus on configuration flags, I will only give a brief overview of the throughput-oriented GC algorithms offered by HotSpot. The GC algorithm is triggered when an object allocation in the old generation fails due to lack of space (usually, the “allocation” is in fact a promotion of an object from the young generation). Starting at so-called “GC roots”, the GC then searches the heap for reachable objects and marks them as alive. Afterwards, the GC moves the live objects within the old generation so that they occupy a single, non-fragmented memory block, and takes a note that the remaining memory area is free. That is, we don’t follow a copying strategy into a different heap area, like the young generation GC algorithm does. Instead, we keep all objects in the same heap area, thereby defragmenting that area. The collectors use one or more threads to perform GC. When more than one thread is used, the different steps of the algorithm are subdivided such that each GC thread mostly works in its own area without interfering with others. During a GC, all application threads are paused, getting restarted only when the GC is finished. Now let us take a look at the most important flags regarding the throughput-oriented GC algorithms.







# References

[Useful JVM Flags – Part 6 (Throughput Collector)](https://blog.codecentric.de/en/2013/01/useful-jvm-flags-part-6-throughput-collector/)

---

created at 2017-08-23 21:56