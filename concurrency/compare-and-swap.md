# Overview

compare-and-swap (CAS) is an **atomic instruction** used in multithreading to achieve synchronization.

It compares the contents of a memory location to a given value and, only if they are the same, modifies the contents of that memory location to a new given value.

# Usage

Algorithms built around CAS typically read some key memory location and remember the old value. Based on that old value, they compute some new value. Then they try to swap in the new value using CAS, where the comparison checks for the location still being equal to the old value. If CAS indicates that the attempt has failed, it has to be repeated from the beginning: the location is re-read, a new value is re-computed and the CAS is tried again.

Instead of immediately retrying after a CAS fails, researchers have found that total system performance can be improved—in multiprocessor systems where many threads constantly update some particular shared variable—if threads that see their CAS fail use *exponential backoff*—in other words, wait a little before retrying the CAS.

# ABA problem

- Some CAS-based algorithms are affected by and must handle the problem of a false positive match, or the ABA problem. It's possible that between the time the old value is read and the time CAS is attempted, some other processors or threads change the memory location two or more times such that it acquires a bit pattern which matches the old value. The problem arises if this new bit pattern, which looks exactly like the old value, has a different meaning: for instance, it could be a recycled address, or a wrapped version counter.
- A common case of the ABA problem is encountered when implementing a lock-free data structure. If an item is removed from the list, deleted, and then a new item is allocated and added to the list, **it is common for the allocated object to be at the same location as the deleted object due to optimization**. A pointer to the new item is thus sometimes equal to a pointer to the old item which is an ABA problem.

### solution

**Use a double-length(two parts consists of a pointer and a counter).**

> A general solution to this is to use a double-length CAS (e.g. on a 32-bit system, a 64-bit CAS). The second half is used to hold a counter. The compare part of the operation compares the previously read value of the pointer *and* the counter, to the current pointer and counter. If they match, the swap occurs - the new value is written - but the new value has an incremented counter. This means that if ABA has occurred, although the pointer value will be the same, ***the counter is exceedingly unlikely to be the same*** (for a 32-bit value, a multiple of 2^32 operations would have to have occurred, causing the counter to wrap and at that moment, the pointer value would have to also by chance be the same).



# Extensions

### Double compare-and-swap (DCAS) 

Compares two unrelated memory locations with two expected values, and if they're equal, sets both locations to new values.

Double compare-and-swap (DCAS or CAS2) is an atomic primitive proposed to support certain concurrent programming techniques. DCAS takes two not necessarily contiguous memory locations and writes new values into them only if they match pre-supplied "expected" values; as such, it is an extension of the much more popular compare-and-swap (CAS) operation.

# Tag

` lock-free` `CAS` `compare and swap ` `automicity`   `ABA`   `retry`  `compare and set` `thread-safe`



# References

[Compare-and-swap](https://en.wikipedia.org/wiki/Compare-and-swap)

[Double compare-and-swap](https://en.wikipedia.org/wiki/Double_compare-and-swap)

[ABA problem](https://en.wikipedia.org/wiki/ABA_problem)

[Package java.util.concurrent.atomic](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/package-summary.html)

[无锁队列的实现](http://coolshell.cn/articles/8239.html)

[用AtomicStampedReference解决ABA问题](http://blog.hesey.net/2011/09/resolve-aba-by-atomicstampedreference.html)

[How CAS (Compare And Swap) in Java works](https://dzone.com/articles/how-cas-compare-and-swap-java)



---

created at 2017-04-13 15:15