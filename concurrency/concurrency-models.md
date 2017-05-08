# Parallel Workers

In the parallel worker concurrency model a delegator distributes the incoming jobs to different workers.Each worker completes the full job. The workers work in parallel, running in different threads, and possibly on different CPUs.

![The parallel worker concurrency model - basic idea.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-1.png)

## Advantages

The advantage of the parallel worker concurrency model is that it is easy to understand. To increase the parallelization of the application you just add more workers.

## Disadvantages

### Shared State Can Get Complex

The shared workers often need access to some kind of shared data, either in memory or in a shared database. 

![The parallel worker concurrency model with shared state illustrated](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-2.png)

As soon as shared state sneaks into the parallel worker concurrency model it starts getting complicated.The threads need to access the shared data in a way that makes sure that changes by one thread are visible to the others (pushed to main memory and not just stuck in the CPU cache of the CPU executing the thread).

Additionally, part of the parallelization is lost when threads are waiting for each other when accessing the shared data structures. Many concurrent data structures are blocking, meaning one or a limited set of threads can access them at any given time. This may lead to contention on these shared data structures. High contention will essentially lead to a degree of serialization of execution of the part of the code that access the shared data structures.

### Stateless Workers

Shared state can be modified by other threads in the system. Therefore workers must re-read the state every time it needs it, to make sure it is working on the latest copy. This is true no matter whether the shared state is kept in memory or in an external database. A worker that does not keep state internally (but re-reads it every time it is needed) is called *stateless* .

Re-reading data every time you need it can get slow. Especially if the state is stored in an external database.

### Job Ordering is Nondeterministic

The job execution order is nondeterministic. There is no way to guarantee which jobs are executed first or last. Job A may be given to a worker before job B, yet job B may be executed before job A.

The nondeterministic nature of the parallel worker model makes it hard to reason about the state of the system at any given point in time. It also makes it harder (if not impossible) to guarantee that one jobs happens before another.







# References

[Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html)

---

created at 2017-05-08 21:24