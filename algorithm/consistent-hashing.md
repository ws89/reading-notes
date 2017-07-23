# Consistent Hashing



## Web Caching

The original motivation for consistent hashing (in 1997) was Web caching.  You’re familiar with  the  concept  and  benefits  of  caching.   In  the  context  of  the  Web,  imagine  a  browser requesting a URL, like `amazon.com` .  Of course, one could request the page from the appropriate Web server.  But if the page is being requested over and over again, it’s wasteful to repeatedly download it from the server.  An obvious idea is to use a Web cache, which stores a local copy of recently visited pages.  When a URL is requested,  one can first check the local cache for the page.  If the page is in the cache, one can send it directly to the browser — no need to contact the original server.  If the page is not in the cache, then the page is downloaded from a suitable server as before, and the result is both sent to the browser and also stored in the local cache for future re-use.

Caching is good. The most obvious benefit is that the end user experiences a much faster response time.  But caches also improve the Internet as a whole:  fewer requests to far away servers means less network traffic, less congestion in the queues at network switches and Web servers, fewer dropped packets, etc.

So  clearly  we  want  Web  caches.   Where  should  they  go?   A  first  idea  is  to  give  each end  user  their  own  cache,  maintained  on  their own  machine  or  device.   So  if  you  request `amazon.com` , and you also requested it in the recent past, then the page can be served from your local cache.  If not, you incur a cache miss, and the page is downloaded and stored in your local cache.

However,  we could take the benefit of caching to the next level if we could implement a  Web  cache  that  is shared by many users ,  for  example,  all  users  of  Stanford’s  network. For example, if you haven’t accessed `amazon.com` recently but someone “nearby” has (e.g.,someone else in the Stanford network), wouldn’t it be cool if you could just use their local copy?   The  benefits  should  be  clear:   by  aggregating  the  recent  page  requests  of  a  large number of users, these users will enjoy many more cache hits and consequently less latency. Akamai’s goal was to take the daydream of a single logical Web cache shared by tons of users and turn it into a viable technology.

Why isn’t this an easy problem?  We focus on one of several obstacles.  Namely, remembering the recently accessed Web pages of a large number of users might take a lot of storage. Unless we want to resort to a big, slow, and special-purpose machine for this purpose, this means that the aggregated cache might not fit on a single machine.  Thus, implementing a shared cache at a large scale requires *spreading the cache over multiple machines*.

Now suppose a browser requests `amazon.com` and you want to know if the Web page has been cached locally.  Suppose the shared cache is spread over 100 machines.  Where should you look for a cached copy of the Web page?  You could poll all 100 caches for a copy, but that  feels  pretty  dumb.   And  with  lots  of  users  and  caches,  this  solution  crosses  the  line from dumb to infeasible.  Wouldn’t it be nice if, instead, given a URL (like `amazon.com` ) we magically knew which cache (like #23) we should look to for a copy?



## A Simple Solution Using Hashing

![](/attachments/algorithm/consistent-hashing-figure-1.png)

Formally,  we  want  a  mapping  from  URLs  to  caches.   The  first  thought  of  a  well-trained computer  scientist  might  be  to  use  a  hash  function  for  this  purpose. Recall  that  a hash function maps elements of a (usually super-big) universe U , like URLs, to “buckets,” such as 32-bit values (Figure 1).

 A “good” hash function h satisfies two properties:

1. It is easy to remember and evaluate.  Ideally, computing the function involves just a few arithmetic operations, and maybe a “mod” operation.


2. For all practical purposes, h behaves like a totally random function,  spreading data out evenly and without noticeable correlation across the possible buckets.

Taking the existence of a good hash function h for granted, we can solve the problem of mapping URLs to caches.  Say there are n caches, named

{ 0,1,2, . . . , n−1} .  Then we can just store the Web page with URL x at the server named ` h(x) mod n` .



Note that h(x) is probably something like a 32-bit value, representing an integer that is way way bigger than n — this is the reason we apply the “ mod n ” operation to recover the name of one of the caches. Suppose the number n of servers is not static,  but rather is changing over time.  For example,  in Akamai’s early days,  they  were  focused  on  adding  as  many  caches  as  possible  all  over  the  Internet,  so n was constantly increasing.  Web caches can also fail or lose connection to the network, which causes n to decrease.

Suppose we add a new cache and thereby bump up n from 100 to 101.  For an object x , it is very unlikely that h(x) mod 100 and h(x) mod 101 are the same number, Thus, changing n *forces almost all objects to relocate*. **This is a disaster for applications where n is constantly changing**.



## Consistent Hashing







# References

[CS168: The Modern Algorithmic Toolbox Lecture #1: Introduction and Consistent Hashing](http://theory.stanford.edu/~tim/s16/l/l1.pdf)

---

created at 2017-07-21 16:52