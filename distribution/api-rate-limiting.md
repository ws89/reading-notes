# When Use

If your users can afford to change the pace at which they hit your API endpoints without affecting the outcome of their requests, then a rate limiter is appropriate. 

# Strategies

### Load shedder

We sometimes need to **drop low-priority requests to make sure that more critical requests get through**. This is called *load shedding*. 

A *load shedder* **makes its decisions based on the whole state of the system** rather than the user who is making the request. Load shedders
 help you deal with emergencies, since they **keep the core part of your business working** while the rest is on fire.



### Request rate limiter

This rate limiter restricts each user to *N* requests per second.

![](https://stripe.com/img/blog/posts/rate-limiters/1-request-rate-limiter.svg)

### Concurrent requests limiter

Instead of “You can use our API 1000 times a second”, this rate limiter says “You can only have 20 API requests in progress at the same time”. 

Some endpoints are much more resource-intensive than others, and users often get frustrated waiting for the endpoint to return and then retry. 
These retries add more demand to the already overloaded resource, slowing things down even more. 

Helps us keep control of our CPU-intensive API endpoints.Before we started using a concurrent requests limiter, we regularly dealt with **resource contention** on our most expensive endpoints caused by users making too many requests at one time. 

It asks your users to use a different programming model of “Fork off X jobs and have them process the queue” compared to “Hammer the API and back off when I get a HTTP 429”. 

![](https://stripe.com/img/blog/posts/rate-limiters/2-concurrent-requests-limiter.svg)

### Fleet usage load shedder

Using this type of load shedder ensures that a certain percentage of your fleet will always be available for your most important API requests.

We divide up our traffic into two types: **critical API methods** (e.g. creating charges) and **non-critical methods** (e.g. listing charges.) We have a Redis cluster that counts how many requests we currently have of each type.

We always reserve a fraction of our infrastructure for critical requests. If our reservation number is 20%, then any non-critical request over their 80% allocation would be rejected with status code 503.

![](https://stripe.com/img/blog/posts/rate-limiters/3-fleet-usage-load-shedder.svg)

### Worker utilization load shedder

 Most API services use a set of workers to independently respond to incoming requests in a parallel fashion. This load shedder is the final line of defense.If your workers start getting backed up with requests, then **this will shed lower-priority traffic.**

We track the number of workers with available capacity at all times. If a box is too busy to handle its request volume, it will slowly start shedding less-critical requests, starting with test mode traffic.If shedding test mode traffic gets it back into a good state, great! We can start to slowly bring traffic back. Otherwise, it’ll escalate and start shedding even more traffic.

**It’s very important that shedding and bringing load happen slowly, or you can end up flapping**(“I got rid of testmode traffic! Everything is fine! I brought it back! Everything is awful!”).

![](https://stripe.com/img/blog/posts/rate-limiters/4-worker-utilization-load-shedder.svg)

# Practice

We use the [token bucket algorithm](https://en.wikipedia.org/wiki/Token_bucket) to do rate limiting. This algorithm has a centralized bucket host where you take tokens on each request, and slowly drip more tokens into the bucket. If the bucket is empty, reject the request. In our case, every Stripe user has a bucket, and every time they make a request we remove a token from that bucket.

### Token bucket

The token bucket algorithm can be conceptually understood as follows:

- A token is added to the bucket every   `1/r`  ![1/r](https://wikimedia.org/api/rest_v1/media/math/render/svg/2ab96580d23ec5eff6bb0e666531eccb7a8035d6) seconds.
- The bucket can hold at the most *b* tokens. If a token arrives when the bucket is full, it is discarded.
- When a packet (network layer [PDU](https://en.wikipedia.org/wiki/Protocol_data_unit)) of *n* bytes arrives, *n* tokens are removed from the bucket, and the packet is sent to the network.
- If fewer than *n* tokens are available, no tokens are removed from the bucket, and the packet is considered to be *non-conformant*.

### Leaky bucket

- It is implemented as a single-server queue with constant service time.
- If the bucket (buffer) overflows then packets are discarded.
- In this algorithm the input rate can vary but the output rate remains constant.

# References

[Scaling your API with rate limiters](https://stripe.com/blog/rate-limiters)

[如何设计API的限流(Rate Limit)功能：4种类型限流器图解](http://mp.weixin.qq.com/s/jIhLGdWl02GppDmfiNuDhw)

[Token bucket](https://en.wikipedia.org/wiki/Token_bucket)



---

created at 2017-04-25 15:50