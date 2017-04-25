# Token bucket

The token bucket algorithm can be conceptually understood as follows:

- A token is added to the bucket every   `1/r`  ![1/r](https://wikimedia.org/api/rest_v1/media/math/render/svg/2ab96580d23ec5eff6bb0e666531eccb7a8035d6) seconds.
- The bucket can hold at the most *b* tokens. If a token arrives when the bucket is full, it is discarded.
- When a packet (network layer [PDU](https://en.wikipedia.org/wiki/Protocol_data_unit)) of *n* bytes arrives, *n* tokens are removed from the bucket, and the packet is sent to the network.
- If fewer than *n* tokens are available, no tokens are removed from the bucket, and the packet is considered to be *non-conformant*.

![](https://image.slidesharecdn.com/trafficshapingpresentation-140126060026-phpapp01/95/leaky-bucket-tocken-bucket-traffic-shaping-8-638.jpg?cb=1390716192)



# Leaky bucket

- It is implemented as a single-server queue with constant service time.
- If the bucket (buffer) overflows then packets are discarded.
- In this algorithm the input rate can vary but the output rate remains constant.

![](https://image.slidesharecdn.com/trafficshapingpresentation-140126060026-phpapp01/95/leaky-bucket-tocken-bucket-traffic-shaping-3-638.jpg?cb=1390716192)

# Versus

| TOKEN BUCKET                             | LEAKY BUCKET                             |
| ---------------------------------------- | ---------------------------------------- |
| Token dependent.                         | Token independent.                       |
| If bucket is full token are discarded, but not the packet. | If bucket is full packet or data is discarded. |
| Packets can only transmitted when there are enough token | Packets are transmitted continuously.    |
| It allows large bursts to be sent faster rate after that constant rate | It sends the packet at constant rate     |
| It saves token to send large bursts.     | It does not save token.                  |

# References

[Token bucket](https://en.wikipedia.org/wiki/Token_bucket)

[Leaky Bucket & Tocken Bucket - Traffic shaping](https://www.slideshare.net/vimal25792/leaky-bucket-tocken-buckettraffic-shaping)



---

created at 2017-04-25 23:40