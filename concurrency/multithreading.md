# Benefits

## Better resource utilization

In general, the CPU can be doing other things while waiting for IO. It doesn't have to be disk IO. It can be network IO as well, or input from a user at the machine. Network and disk IO is often a lot slower than CPU's and memory IO.

### Example:

Imagine an application that reads and processes files from the local file system.Lets say that reading af file from disk takes 5 seconds and processing it takes 2 seconds.  Processing two files then takes

```
  5 seconds reading file A
  2 seconds processing file A
  5 seconds reading file B
  2 seconds processing file B
-----------------------
 14 seconds total
```

When reading the file from disk most of the CPU time is spent waiting for the disk to read the data. The CPU is pretty much idle during that time. It could be doing something else. By changing the order of the operations, the CPU could be better utilized. Look at this ordering:

```
  5 seconds reading file A
  5 seconds reading file B + 2 seconds processing file A
  2 seconds processing file B
-----------------------
 12 seconds total
```

The CPU waits for the first file to be read. Then it starts the read of the second file. While the second file is being read, the CPU processes the first file. Remember, while waiting for the file to be read from disk, the CPU is mostly idle.



## More responsive programs

### Example

Another common goal for turning a singlethreaded application into a multithreaded application is to achieve a more responsive application. Imagine a server application that listens on some port for incoming requests. when a request is received, it handles the request and then goes back to listening. The server loop is sketched below:

```
while(server is active){
  listen for request
  process request
}
```

If the request takes a long time to process, no new clients can send requests to the server for that duration. Only while the server is listening can requests be received.

An alternate design would be for the listening thread to pass the request to a worker thread, and return to listening immediatedly. The worker thread will process the request and send a reply to the client. This design is sketched below:

```
while(server is active){
  listen for request
  hand request to worker thread
}
```

This way the server thread will be back at listening sooner. Thus more clients can send requests to the server. The server has become more responsive.



# Costs

## More complex design

Though some parts of a multithreaded applications is simpler than a singlethreaded application, other parts are more complex. Code executed
by multiple threads accessing shared data need special attention. Thread interaction is far from always simple. Errors arising from incorrect thread synchronization can be very hard to detect, reproduce and fix.



## Context Switching Overhead

When a CPU switches from executing one thread to executing another, the CPU needs to save the local data, program pointer etc. of the current thread,and load the local data, program pointer etc. of the next thread to execute.This switch is called a "context switch". 

Context switching isn't cheap. You don't want to switch between threads more than necessary. 

![https://upload.wikimedia.org/wikipedia/commons/0/04/Context_switch.png](https://upload.wikimedia.org/wikipedia/commons/0/04/Context_switch.png)



## Increased Resource Consumption

A thread needs some resources from the computer in order to run. Besides CPU time a thread needs some memory to keep its local stack.It may also take up some resources inside the operating system needed to manage the thread.



# References

[Multithreading Benefits](http://tutorials.jenkov.com/java-concurrency/benefits.html)

[Multithreading Costs](http://tutorials.jenkov.com/java-concurrency/costs.html)

---

created at 2017-05-08 19:18