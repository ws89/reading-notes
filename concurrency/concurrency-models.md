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



# Assembly Line

The second concurrency model is what I call the ***assembly line*** concurrency model. I chose that name just to fit with the "parallel worker" metaphor from earlier. Other developers use other names (e.g. ***reactive systems***, or ***event driven systems***) depending on the platform / community. 

![The assembly line concurrency model.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-3.png)

The workers are organized like workers at an assembly line in a factory. **Each worker only performs a part of the full job. When that part is finished the worker forwards the job to the next worker.**

Each worker is running in its own thread, and shares no state with other workers. This is also sometimes referred to as a *shared nothing* concurrency model.

### Non-blocking IO

Systems using the assembly line concurrency model are usually designed to use non-blocking IO. 


Non-blocking IO means that when a worker starts an IO operation (e.g. reading a file or data from a network connection) the worker does not wait for the IO call to finish. IO operations are slow, so waiting for IO operations to complete is a waste of CPU time.The CPU could be doing something else in the meanwhile. When the IO operation finishes, the result of the IO operation (e.g. data read or status of data written) is passed on to another worker.

![The assembly line concurrency model with non-blocking IO operations marking the boundaries between worker responsibility.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-4.png)

With non-blocking IO, the IO operations determine the boundary between workers. A worker does as much as it can until it has to start an IO operation. Then it gives up control over the job. When the IO operation finishes, the next worker in the assembly line continues working on the job, until that too has to start an IO operation etc.



### Multi assembly lines




![The assembly line concurrency model with multiple assembly lines.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-5.png)

In reality, the jobs may not flow along a single assembly line. Since most systems can perform more than one job,jobs flows from worker to worker depending on the job that needs to be done. In reality there could be multiple different virtual assembly lines going on at the same time. 



![The assembly line concurrency model showing jobs forwarded to multiple workers.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-6.png)

Jobs may even be forwarded to more than one worker for concurrent processing. For instance, a job may be forwarded to both a job executor and a job logger. This diagram illustrates how all three assembly lines finish off by forwarding their jobs to the same worker (the last worker in the middle assembly line).



## Reactive, Event Driven Systems

The system's workers react to events occurring in the system, either received from the outside world or emitted by other workers. Examples of events could be an incoming HTTP request,or that a certain file finished loading into memory etc.

- [Vert.x](http://tutorials.jenkov.com/vert.x/index.html)
- Akka
- Node.JS (JavaScript)



### Actors vs. Channels

![The assembly line concurrency model implemented using actors.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-7.png)

In the actor model each worker is called an *actor*. Actors can send messages directly to each other. Messages are sent and processed asynchronously. Actors can be used to implement one or more job processing assembly lines.



![The assembly line concurrency model implemented using channels.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-8.png)

In the channel model, workers do not communicate directly with each other. Instead they publish their messages (events) on different channels. Other workers can then listen for messages on these channels without the sender knowing who is listening.

A worker does not need to know about what workers will process the job later in the assembly line. It just needs to know what channel to forward the job to (or send the message to etc.). Listeners on channels can subscribe and unsubscribe without affecting the workers writing to the channels. This allows for a somewhat looser coupling between workers.



## Advantages

### No Shared State

The fact that workers share no state with other workers means that they can be implemented without having to think about all the concurrency problems that may arise from concurrent access to shared state. This makes it much easier to implement workers. You implement a worker as if it was the only thread performing that work - essentially a singlethreaded implementation.

### Stateful Workers

Since workers know that no other threads modify their data, the workers can be stateful. By stateful I mean that they can keep the data they need to operate in memory, only writing changes back the eventual external storage systems. A stateful worker can therefore often be faster than a stateless worker.

### Better Hardware Conformity

Singlethreaded code has the advantage that it often conforms better with how the underlying hardware works.

First of all, you can usually create more optimized data structures and algorithms when you can assume the code is executed in single threaded mode.

Second, singlethreaded stateful workers can cache data in memory as mentioned above. When data is cached in memory there is also a higher probability that this data is also cached in the CPU cache of the CPU executing the thread.This makes accessing cached data even faster.

### Job Ordering is Possible

It is possible to implement a concurrent system according to the assembly line concurrency model in a way that guarantees job ordering.Job ordering makes it much easier to reason about the state of a system at any given point in time.Furthermore, you could write all incoming jobs to a log. This log could then be used to rebuild the state of the system from scratch in case any part of the system fails. The jobs are written to the log in a certain order, and this order becomes the guaranteed job order. 

![The assembly line concurrency model with a job logger.](http://tutorials.jenkov.com/images/java-concurrency/concurrency-models-8.png)

Implementing a guaranteed job order is not necessarily easy, but it is often possible. If you can, it greatly simplifies tasks like backup, restoring data, replicating data etc. as this can all be done via the log file(s).



## Disadvantages

The main disadvantage of the assembly line concurrency model is that the execution of a job is often spread out over multiple workers, and thus over multiple classes in your project. Thus it becomes harder to see exactly what code is being executed for a given job.

It may also be harder to write the code. Worker code is sometimes written as callback handlers. Having code with many nested callback handlers may result in what some developer call *callback hell*. Callback hell simply means that it gets hard to track what the code is really doing across all the callbacks, as well as making sure that each callback has access to the data it needs.



# Functional Parallelism

The basic idea of functional parallelism is that you implement your program using function calls.Functions can be seen as "agents" or "actors" that send messages to each other.When one function calls another, that is similar to sending a message.

All parameters passed to the function are copied, so no entity outside the receiving function can manipulate the data. This copying is essential to avoiding race conditions on the shared data. This makes the function execution similar to an atomic operation. Each function call can be executed independently of any other function call.

When each function call can be executed independently, each function call can be executed on separate CPUs. That means, that an algorithm implemented functionally can be executed in parallel, on multiple CPUs.

With Java 7 we got the `java.util.concurrent` package contains the [ForkAndJoinPool](http://tutorials.jenkov.com/java-util-concurrent/java-fork-and-join-forkjoinpool.html) which can help you implement something similar to functional parallelism. With Java 8 we got parallel [streams](http://tutorials.jenkov.com/java-collections/streams.html) which can help you parallelize the iteration of large collections. 

The hard part about functional parallelism is knowing which function calls to parallelize. Coordinating function calls across CPUs comes with an overhead. The unit of work completed by a function needs to be of a certain size to be worth this overhead. If the function calls are very small, attempting to parallelize them may actually be slower than a singlethreaded, single CPU execution.



# References

[Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html)

---

created at 2017-05-08 21:24