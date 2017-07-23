# Consistent Hashing



## Web Caching

The original motivation for consistent hashing (in 1997) was Web caching.  You’re familiar with  the  concept  and  benefits  of  caching.   In  the  context  of  the  Web,  imagine  a  browser requesting a URL, like `amazon.com` .  Of course, one could request the page from the appropriate Web server.  But if the page is being requested over and over again, it’s wasteful to repeatedly download it from the server.  An obvious idea is to use a Web cache, which stores a local copy of recently visited pages.  When a URL is requested,  one can first check the local cache for the page.  If the page is in the cache, one can send it directly to the browser — no need to contact the original server.  If the page is not in the cache, then the page is downloaded from a suitable server as before, and the result is both sent to the browser and also stored in the local cache for future re-use.

Caching is good. The most obvious benefit is that the end user experiences a much faster response time.  But caches also improve the Internet as a whole:  fewer requests to far away servers means less network traffic, less congestion in the queues at network switches and Web servers, fewer dropped packets, etc.

So  clearly  we  want  Web  caches.   Where  should  they  go?   A  first  idea  is  to  give  each end  user  their  own  cache,  maintained  on  their own  machine  or  device.   So  if  you  request `amazon.com` , and you also requested it in the recent past, then the page can be served from your local cache.  If not, you incur a cache miss, and the page is downloaded and stored in your local cache.

However,  we could take the benefit of caching to the next level if we could implement a  Web  cache  that  is shared by many users ,  for  example,  all  users  of  Stanford’s  network. For example, if you haven’t accessed `amazon.com` recently but someone “nearby” has (e.g.,someone else in the Stanford network), wouldn’t it be cool if you could just use their local copy?   The  benefits  should  be  clear:   by  aggregating  the  recent  page  requests  of  a  large number of users, these users will enjoy many more cache hits and consequently less latency. Akamai’s goal was to take the daydream of a single logical Web cache shared by tons of users and turn it into a viable technology.

Why isn’t this an easy problem?  We focus on one of several obstacles.  Namely, remembering the recently accessed Web pages of a large number of users might take a lot of storage. Unless we want to resort to a big, slow, and special-purpose machine for this purpose, this means that the aggregated cache might not fit on a single machine.  Thus, implementing a shared cache at a large scale requires *spreading the cache over multiple machines*.

Now suppose a browser requests `amazon.com` and you want to know if the Web page has been cached locally.  Suppose the shared cache is spread over 100 machines.  Where should you look for a cached copy of the Web page?  You could poll all 100 caches for a copy, but that  feels  pretty  dumb.   And  with  lots  of  users  and  caches,  this  solution  crosses  the  line from dumb to infeasible.  Wouldn’t it be nice if, instead, given a URL (like `amazon.com` ) we magically knew which cache (like #23) we should look to for a copy?

![](/attachments/algorithm/consistent-hashing-figure-1.png)







# References

[CS168: The Modern Algorithmic Toolbox Lecture #1: Introduction and Consistent Hashing](http://theory.stanford.edu/~tim/s16/l/l1.pdf)

---

created at 2017-07-21 16:52