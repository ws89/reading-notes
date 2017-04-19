# Simple Timing Wheel

![](https://cdn2.hubspot.net/hub/540072/hubfs/purgatory_benchmark/TimingWheels1.png)

A simple timing wheel is a circular list of buckets of timer tasks. 

Let u be the time unit. A timing wheel with size **n** has **n** buckets and can hold timer tasks in **n \* u** time interval. Each bucket holds timer tasks that fall into the corresponding time range. 

At the beginning, the first bucket holds tasks for **[0, u)**, the second bucket holds tasks for **[u, 2u)**, …, the **n**-th bucket for **[u \* (n -1), u * n)**. 

The emptied bucket is then available for the next round, so if the current bucket is for the time t, it becomes the bucket for **[t + u \* n, t + (n + 1) * u)** after a tick. 

A timing wheel has O(1) cost for insert/delete (start-timer/stop-timer) .

#### Drawback 

A simple timing wheel is that it assumes that a timer request is within the time interval of **n \* u** from the current time. If a timer request is out of this interval, it is an overflow. 

# Hierarchical Timing Wheel

![](https://cdn2.hubspot.net/hub/540072/hubfs/purgatory_benchmark/TimingWheels2.png)

It is a hierarchically organized timing wheels that delegate overflows to upper level wheels.

The lowest level has the finest time resolution. Time resolutions become coarser as we move up the hierarchy. 

If the resolution of a wheel at one level is **u** and the size is **n**, the resolutions should be **n \* u** in the second level, **n2 \* u** in the third level, and so on. 

At each level overflows are delegated to the wheel in one level higher. 

When the wheel in the higher level ticks, it reinsert timer tasks to the lower level.An overflow wheel can be created on-demand. 

When a bucket in an overflow bucket expires, all tasks in it are reinserted into the timer recursively. The tasks are then moved to the finer grain wheels or be executed. 

The insert (start-timer) cost is O(m) where **m** is the number of wheels, which is usually very small compared to the number of requests in the system, and the delete (stop-timer) cost is still O(1).



# References

[Apache Kafka, Purgatory, and Hierarchical Timing Wheels](https://www.confluent.io/blog/apache-kafka-purgatory-hierarchical-timing-wheels/)

[Hashed and Hierarchical Timing Wheels: Efficient Data Structures for Implementing a Timer Facility](https://pdfs.semanticscholar.org/e04e/548b287d48260def0ffe09692236d2a56ad3.pdf)

[Hashed and Hierarchical Timing Wheels](https://www.slideshare.net/supperniu/timing-wheels)



---

created at 2017-04-17 16:20