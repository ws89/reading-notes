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

![](/attachments/algorithm/consistent-hashing-figure-2.png)


![](/attachments/algorithm/consistent-hashing-figure-3.png)


The key idea is:  in addition to hashing the names of all objects (URLs) x , like before, we also hash the names of all the servers s .  The object and server names need to be hashed to the same range, such as 32-bit values.

To understand which objects are assigned to which servers, consider the array shown in Figure 2, indexed by the possible hash values.  (This array might be very big and it exists only  in  our  minds;  we’ll  discuss  the  actual  implementation  shortly.)   Imagine  that  we’ve already hashed all the server names and made a note of them in the corresponding buckets. Given an object x that hashes to the bucket h(x), we scan buckets to the right of h(x) until we find a bucket h(s) to which the name of some server s hashes.  (We wrap around the array, if need be.)  We then designate s as the server responsible for the object x .

This approach to consistent hashing can also be visualized on a circle, with points on the circle corresponding to the possible hash values (Figure 3(left)).  Servers and objects both hash to points on this circle; an object is stored on the server that is closest in the clockwise direction.  Thus n servers partition the circle into n segments, with each server responsible for all objects in one of these segments.

This simple idea leads to some nice properties.  First, assuming reasonable hash functions, by symmetry, the expected load on each of the n servers is exactly a 1/n fraction of the objects. (There is non-trivial variance; below we explain how to reduce it via replication.)  Second, and more importantly, suppose we add a new server s — which objects have to move? Only the objects stored at s . See Figure 3(right).  Combined, these  two observations imply that, in  expectation,  adding  the *n*th  server  causes  only  a 1/n fraction  of  the  objects  to  relocate. This  is  the  best-case  scenario  if  we  want  the  load  to  be  distributed  evenly  —  clearly  the objects on the new server have to move from where they were before.  By contrast, with the solution (1), on average only a 1/n fraction of the objects don’t move when the *n*th server is added!



So how do we actually implement the standard hash table operations Lookup and Insert? Given an object x , both operations boil down to the problem of efficiently implementing the rightward/clockwise scan for the server s that minimizes h(s) subject to h(s) ≥ h(x). Thus,we want a data structure for storing the server names, with the corresponding hash values as keys, that supports a fast Successor operation.  A hash table isn’t good enough (it doesn’t maintain any order information at all); a heap isn’t good enough (it only maintains a partial order  so  that  identifying  the  minimum  is  fast);  but  recall  that ***binary search trees*** ,  which maintain a total ordering of the stored elements, do export a Successor function. Since the running time of this operation is linear in the depth of the tree, it’s a good idea to use a balanced binary search tree,  such as a **Red-Black tree**.  Finding the server responsible for storing a given object x then takes O(logn) time, where n is the number of servers.



### Reducing the variance / virtual  copies

![](/attachments/algorithm/consistent-hashing-figure-4.png)

While the expected load of each server is a 1/n fraction of the objects, the realized load of each server will vary.  Pictorially, if you pick n random points on  the  circle,  you’re  very  unlikely  to  get  a  perfect  partition  of  the  circle  into  equal-sized segments.

An  easy  way  to  decrease  this  variance  is  to  make k “**virtual  copies**”  of  each  server s , implemented  by  hashing  its  name  with k different  hash  functions  to  get h1(s), . . . , hk(s).

or example,  with servers {0,1,2} and k= 4, we choose 12 points on the circle — 4 labeled “0”, 4 labeled “1”, and 4 labeled “2”.(See Figure 4.)  Objects are assigned as before — from h(x), we scan rightward/clockwise until we encounter one of the hash values of some server s , and s is responsible for storing x.



By symmetry, each server still expects to get a 1/n fraction of the objects.  This replication increases the number of keys stored in the balanced binary search by a factor of k , but it reduces the variance in load across servers significantly.  Intuitively, some copies of a server will  get  more  objects  than  expected  (more  than  a 1/kn fraction),  but  this  will  be  largely canceled  out  by  other  copies  that  get  fewer  objects  than  expected. Choosing k≈$ \log_2 n $ is  large  enough  to  obtain  reasonably  balanced  loads.  

Virtual copies are also useful for dealing with heterogeneous servers that have different capacities.  The sensible approach is to make the number of virtual copies of a server proportional to the server capacity; for example, if one server is twice as big as another, it should have twice as many virtual copies.




# References

[CS168: The Modern Algorithmic Toolbox Lecture #1: Introduction and Consistent Hashing](http://theory.stanford.edu/~tim/s16/l/l1.pdf)

---

created at 2017-07-21 16:52