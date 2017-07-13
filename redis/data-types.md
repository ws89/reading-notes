# Data types

## Strings

Redis Strings are binary safe, this means that a Redis string can contain any kind of data, for instance a  JPEG image or a serialized Ruby object.

A String value can be at max 512 Megabytes in length.

**Practice:**

- INCR -*[ rate limiter ]*
- INCRBY
- INCRBYFLOAT 


- DECR 
- DECRBY 
- GET 
- GETRANGE
- SETRANGE
- APPEND
- [GETBIT/SETBIT](https://www.zhihu.com/question/27672245)
- GETSET
- MGET
- MSET
- MSETNX 
- PSETEX 
- SET 
- SETEX 
- SETNX 
- SETRANGE 
- STRLEN 


- *BITCOUNT*...
- ​




## Lists

Lists: collections of string elements sorted according to the order of insertion. They are basically ***linked lists***.

Redis Lists are simply lists of strings, sorted by insertion order. It is possible to add elements to a Redis List pushing new elements on the head  (on the left) or on the tail (on the right) of the list.

The [LPUSH](https://redis.io/commands/lpush) command inserts a new element on the head, while  [RPUSH](https://redis.io/commands/rpush) inserts a new element on the tail. 

The max length of a list is 2^32 - 1 elements (4294967295, more than 4 billion of elements per list).

The main features of Redis Lists from the point of view of time complexity are the support for constant time insertion and deletion of elements near the head and tail, even with many millions of inserted items. Accessing elements is very fast near the extremes of the list but is slow if you try accessing the middle of a very big list, as it is an O(N) operation.

Redis lists are implemented via Linked Lists. This means that even if you have millions of elements inside a list, the operation of adding a new element in the head or in the tail of the list is performed *in constant time*. The speed of adding a new element with the [LPUSH](https://redis.io/commands/lpush) command to the head of a list with ten elements is the same as adding an element to the head of list with 10 million elements.



**practice:**

- RPUSH 
- BLPOP
- RPOP
- BRPOP
- [RPOPLPUSH](https://redis.io/commands/rpoplpush)
- BRPOPLPUSH
- LINDEX 
- LINSERT 
- LLEN
- LPOP 
- LPUSH 
- LPUSHX 
- LRANGE 
- LREM 
-  LSET 
- LTRIM 




### BRPOP

Note that you can use 0 as timeout to wait for elements forever, and you can also specify multiple lists and not just one, in order to wait on multiple  lists at the same time, and get notified when the first list receives an element.

A few things to note about [BRPOP](https://redis.io/commands/brpop):

1. Clients are served in an ordered way: the first client that blocked waiting for a list, is served first when an element is pushed by some other client, and so forth.
2. The return value is different compared to [RPOP](https://redis.io/commands/rpop): it is a two-element array since it also includes the name of the key, because [BRPOP](https://redis.io/commands/brpop) and [BLPOP](https://redis.io/commands/blpop) are able to block waiting for elements from multiple lists.
3. If the timeout is reached, NULL is returned.




## Sets

Redis Sets have the desirable property of not allowing repeated members. Adding the same element multiple times will result in a set having a 
single copy of this element. Practically speaking this means that adding  a member does not require a *check if exists then add* operation.

The max number of members in a set is 2^32 - 1 (4294967295, more than 4 billion   of members per set).****

**Practice:**

- SADD
- SCARD 
- SDIFF 
- SDIFFSTORE 


- SINTER 
- SINTERSTORE 
- SISMEMBER 
- SMEMBERS 
- SMOVE 
- SPOP 
- SRANDMEMBER
- SREM 
- SUNION
- SUNIONSTORE 






## Sorted Set

Redis sorted sets use a *double 64-bit floating point number* to represent the score. 

Sorted sets are implemented via a dual-ported data structure containing both a skip list and a hash table, so every time we add an element Redis performs an O(log(N)) operation.

**practice:**

- ZRANGE
- ZREVRANGE 
- ZINCRBY 
- ZSCORE 
- ZADD 
- ZRANK 
- ZREVRANK 
- ZCARD 
- ZCOUNT 
- ZUNIONSTORE 
- ZINTERSTORE 
- ZLEXCOUNT 
- [ZRANGEBYLEX](http://www.redis.cn/commands/zrangebylex.html) 
- ZRANGEBYSCORE
- ZREM 
- ZREMRANGEBYLEX 
- ZREMRANGEBYRANK 
- ZREMRANGEBYSCORE 
- ZREVRANGE 
- ZREVRANGEBYLEX 
- ZREVRANGEBYSCORE 





## Hash

Every hash can store up to 2^32 - 1 field-value pairs (more than 4 billion).

Redis Hashes are maps between string fields and string values, so they are the perfect data type to represent objects (e.g. A User with a number of fields like name, surname, age, and so forth).

**practice:**

- HMSET 
- HGETALL 
- HSET 
- HGET 
- HDEL 
- HEXISTS 
- HINCRBY 
- HINCRBYFLOAT 
- HKEYS 
- HLEN 
- HMGET 
- HSETNX 
- HSTRLEN 
- HVALS 




## Common Commands 

**Practice:**

- TTL
- PTTL
- PERSIST
- TYPE




# References

[Data types](https://redis.io/topics/data-types)

[An introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro)

[Inclusive in math](http://www.mathwords.com/i/inclusive.htm)

[ZRANGEBYLEX ](http://www.redis.cn/commands/zrangebylex.html)

---

created at 2017-07-08 20:34