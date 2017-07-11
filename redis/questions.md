# Thread-safe

[Are redis operations on data structures thread safe](https://stackoverflow.com/questions/17099222/are-redis-operations-on-data-structures-thread-safe)

**Redis is actually single-threaded**, which is how every command is guaranteed to be atomic.**While one command is executing, no other command will run**.



[Redis is single-threaded, then how does it do concurrent I/O?](https://stackoverflow.com/questions/10489298/redis-is-single-threaded-then-how-does-it-do-concurrent-i-o)

A single-threaded program can definitely **provides concurrency *at the I/O level* by using an I/O (de)multiplexing mechanism and an event loop** (which is what Redis does).

**The fact that Redis operations are atomic is simply a consequence of the single-threaded event loop.** The interesting point is atomicity is provided at no extra cost (it does not require synchronization). It can be exploited by the user to implement optimistic locking and other patterns without paying for the synchronization overhead.



[Is Redis list or set pop method thread safe?](https://stackoverflow.com/questions/11658272/is-redis-list-or-set-pop-method-thread-safe)

so as every command gets queued in a single thread you should be ok as there will never be two commands executing in parallel.







---

created at 2017-07-11 21:51