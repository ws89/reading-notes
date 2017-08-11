## What is event multiplexing?

How can we achieve I/O multiplexing without thread-per-connection? You can simply do busy-wait polling for each connection with non-blocking 
socket operations, but this is too wasteful. What we need to know is which socket becomes ready. So the OS kernel provides a separate channel  between your application and the kernel, and this channel notifies when some of your sockets become ready. This is how select()/poll() works,  based on *the readiness model*.



We want to be notified if one or more I/O conditions are ready (i.e., input is ready to be read, or the descriptor is capable of taking more output). This capability is called **I/O multiplexing** and is provided by the `select` and `poll` functions, as well as a newer POSIX variation of the former, called `pselect`.



### I/O Models

We first examine the basic differences in the five I/O models that are available to us under Unix:

- blocking I/O
- nonblocking I/O
- I/O multiplexing (`select` and `poll`)
- signal driven I/O (`SIGIO`)
- asynchronous I/O (the POSIX `aio_` functions)



There are normally two distinct phases for an input operation:

1. Waiting for the data to be ready. This involves waiting for data to arrive on the network. When the packet arrives, it is copied into a buffer within the kernel.
2. Copying the data from the kernel to the process. This means copying the (ready) data from the kernel's buffer into our application buffer



#### Blocking I/O Model

![Figure 6.1. Blocking I/O model.](https://notes.shichao.io/unp/figure_6.1.png)

We say that the process is blocked the entire time from when it calls `recvfrom` until it returns. When `recvfrom` returns successfully, our application processes the datagram.



#### Nonblocking I/O Model

When a socket is set to be nonblocking, we are telling the kernel "when an I/O operation that I request cannot be completed without putting the 
process to sleep, do not put the process to sleep, but return an error instead". The figure is below:

​	![Figure 6.2. Nonblocking I/O model.](https://notes.shichao.io/unp/figure_6.2.png)

- For the first three `recvfrom`, there is no data to return and the kernel immediately returns an error of `EWOULDBLOCK`.
- For the fourth time we call recvfrom, a datagram is ready, it is copied into our application buffer, and `recvfrom` returns successfully. We then process the data.

When an application sits in a loop calling `recvfrom` on a nonblocking descriptor like this, it is called **polling**. The application is continually polling the kernel to see if some operation is ready. ***This is often a waste of CPU time***, but this model is occasionally encountered, normally on systems dedicated to one function.



#### I/O Multiplexing Model

With **I/O multiplexing**, we call `select` or `poll` and block in one of these two system calls, instead of blocking in the actual I/O system call. The figure is a summary of the I/O multiplexing model:

![Figure 6.3. I/O multiplexing model.](https://notes.shichao.io/unp/figure_6.3.png)

We block in a call to `select`, waiting for the datagram socket to be readable. When `select` returns that the socket is readable, we then call `recvfrom` to copy the datagram into our application buffer.

##### Comparing to the blocking I/O model

- Disadvantage: using `select` requires two system calls (`select` and `recvfrom`) instead of one
- Advantage: **we can wait for more than one descriptor to be ready** (see [the `select` function](https://notes.shichao.io/unp/ch6/#select-function) later in this chapter)

##### Multithreading with blocking I/O

Another closely related I/O model is to use multithreading with blocking I/O. That model very closely resembles the model described above, 
except that instead of using `select` to block on multiple file descriptors, the program uses multiple threads (one per file descriptor), and each thread is then free to call blocking system calls like `recvfrom`.



#### Signal-Driven I/O Model

The **signal-driven I/O model** uses signals, telling the kernel to notify us with the `SIGIO` signal when the descriptor is ready. The figure is below:

![Figure 6.4. Signal-Driven I/O model.](https://notes.shichao.io/unp/figure_6.4.png)

- We first enable the socket for signal-driven I/O ([Section 25.2](https://notes.shichao.io/unp/ch6/ch25.md)) and install a signal handler using the `sigaction` system call. The return from this system call is immediate and our process continues; it is not blocked.

- When the datagram is ready to be read, the `SIGIO`  signal is generated for our process. We can either:

  - read the datagram from the signal handler by calling `recvfrom` and then notify the main loop that the data is ready to be processed ([Section 25.3](https://notes.shichao.io/unp/ch6/ch25.md))
  - notify the main loop and let it read the datagram.


The advantage to this model is that we are not blocked while waiting for the datagram to arrive. The main loop can continue executing and just 
wait to be notified by the signal handler that either the data is ready to process or the datagram is ready to be read.



#### Asynchronous I/O Model

These functions work by telling the kernel to start the operation and to notify us when the entire operation (including the copy of the data from the kernel to our buffer) is complete. The main difference between this model and the signal-driven I/O model is that with signal-driven I/O, the kernel tells us when an I/O operation can be initiated, but with asynchronous I/O, **the kernel tells us when an I/O operation is complete**. See the figure below for example:

![Figure 6.5. Asynchronous I/O model.](https://notes.shichao.io/unp/figure_6.5.png)

- We call `aio_read` (the POSIX asynchronous I/O functions begin with `aio_` or `lio_`) and pass the kernel the following:
  - descriptor, buffer pointer, buffer size (the same three arguments for `read`)
  - file offset (similar to `lseek`)
  - and how to notify us when the entire operation is complete

  This system call returns immediately and our process is not blocked while waiting for the I/O to complete.

- We assume in this example that we ask the kernel to generate some signal when the operation is complete. This signal is not generated until the data has been copied into our application buffer, which is different from the signal-driven I/O model.



#### Comparison of the I/O Models

![Figure 6.6. Comparison of the five I/O models.](https://notes.shichao.io/unp/figure_6.6.png)

The main difference between the first four models is the first phase, as the second phase in the first four models is the same: the process is 
blocked in a call to `recvfrom` while the data is copied from the kernel to the caller's buffer. Asynchronous I/O, however, handles both phases and is different from the first four.




#### Synchronous I/O versus Asynchronous I/O

POSIX defines these two terms as follows:

- A synchronous I/O operation causes the requesting process to be blocked until that I/O operation completes.
- An asynchronous I/O operation does not cause the requesting process to be blocked.

Using these definitions, the first four I/O models (blocking, nonblocking, I/O multiplexing, and signal-driven I/O) are all synchronous because the actual I/O operation (`recvfrom`) blocks the process. Only the asynchronous I/O model matches the asynchronous I/O definition.



# References

[Scalable Event Multiplexing: epoll vs. kqueue](http://people.eecs.berkeley.edu/~sangjin/2012/12/21/epoll-vs-kqueue.html)

[Chapter 6. I/O Multiplexing: The select and poll Functions](https://notes.shichao.io/unp/ch6/#chapter-6-io-multiplexing-the-select-and-poll-functions)

[6.2 I/O Models](http://www.masterraghu.com/subjects/np/introduction/unix_network_programming_v1.3/ch06lev1sec2.html)

[6.3 `select` Function](http://www.masterraghu.com/subjects/np/introduction/unix_network_programming_v1.3/ch06lev1sec3.html)

---

created at 2017-07-11 22:50

updated at 2017-08-10 23:40