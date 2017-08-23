# Throughput Collector

For most application areas that we find in practice, a garbage collection (GC) algorithm is being evaluated according to two criteria:

1. The higher the achieved throughput, the better the algorithm.
2. The smaller the resulting pause times, the better the algorithm.



### throughput

The JVM always executes a GC in dedicated threads, the “GC threads”. Whenever GC threads are active, they compete against the actual “application threads” for available processors and thus CPU time. Simplified just a little bit, we refer by “throughput” to the fraction of total program execution time at which the application threads are running. For example, a throughput of 99/100 means that on average the application threads are running 99 out of 100 seconds of program execution time, while the GC threads are only running for one second during the same time span.



### pause time

The term “pause time” refers to a time span in which the **application threads are paused completely** in favor of the GC threads. For example, a pause time of 100 milliseconds during a GC means that no application thread was active during that 100 millisecond interval. If we then refer to an “average pause time” of 100 milliseconds for a running application, we state that all pause times observed for that application had an average length of 100 milliseconds. Similarly, a “maximum pause time” of 100 milliseconds means that none of the pause times observed were larger than 100 milliseconds.





# References

[Useful JVM Flags – Part 6 (Throughput Collector)](https://blog.codecentric.de/en/2013/01/useful-jvm-flags-part-6-throughput-collector/)

---

created at 2017-08-23 21:56