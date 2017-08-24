# CMS Collector

The **Concurrent Mark Sweep** Collector (“CMS Collector”) of the HotSpot JVM has one primary goal: low application pause times. This goal is important for most interactive applications like web applications. 



Just like the Throughput Collector (see [part 6](http://blog.codecentric.de/en/2013/01/useful-jvm-flags-part-6-throughput-collector/) of the series), the CMS Collector handles objects in the **old generation**, yet its operation is much more complex. The Throughput Collector always pauses the application threads, possibly for a considerable amount of time, which however enables its algorithms to safely ignore the application. In contrast to that, the CMS Collector is designed to **run mostly concurrent to the application threads and only cause few (and short) pause times**. The downside of running GC concurrently to the application is that various synchronization and data inconsistency issues may arise. In order to achieve safe and correct concurrent execution, a GC cycle of the CMS Collector is divided into a number of consecutive phases.



## Phases of the CMS Collector

A GC cycle of the CMS Collector consists of **six phases**. Four of the phases (the names of which start with „Concurrent“) are run concurrently to the actual application while the other two phases need to stop the 
application threads.





# References

[Useful JVM Flags – Part 7 (CMS Collector)](https://blog.codecentric.de/en/2013/10/useful-jvm-flags-part-7-cms-collector/)

[[8 Concurrent Mark Sweep (CMS) Collector]()](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html)

[Understanding GC pauses in JVM, HotSpot's CMS collector.](http://blog-archive.griddynamics.com/2011/06/understanding-gc-pauses-in-jvm-hotspots_02.html)

---

created at 2017-08-24 23:25