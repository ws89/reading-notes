# EXPIRE

- The timeout will only be cleared by commands that delete or overwrite the contents of the key, including [DEL](https://redis.io/commands/del), [SET](https://redis.io/commands/set), [GETSET](https://redis.io/commands/getset) and all the `*STORE`  commands.
- This means that all the operations that conceptually *alter* the value stored at the key without replacing it with a new one will leave the timeout untouched. For instance, incrementing the value of a key with [INCR](https://redis.io/commands/incr), pushing a new value into a list with [LPUSH](https://redis.io/commands/lpush), or altering the field value of a hash with [HSET](https://redis.io/commands/hset) are all operations that will leave the timeout untouched.
- The timeout can also be cleared, turning the key back into a persistent key, using the [PERSIST](https://redis.io/commands/persist) command.
- If a key is renamed with [RENAME](https://redis.io/commands/rename), the associated time to live is transferred to the new key name.



## Expires and persistence

Keys expiring information is stored as **absolute Unix timestamps** (in milliseconds in case of Redis version 2.6 or greater). This means that the time is flowing even when the Redis instance is not active.

For expires to work well, **the computer time must be taken stable**. If you move an RDB file from two computers with a big desync in their clocks, funny things may happen (like all the keys loaded to be expired at loading time).

Even running instances will always check the computer clock, so for instance if you set a key with a time to live of 1000 seconds, and then set your computer time 2000 seconds in the future, the key will be expired immediately, instead of lasting for 1000 seconds.



## How Redis expires keys

Redis keys are expired in two ways: a **passive** way, and an **active** way.

A key is passively expired simply **when some client tries to access it, and the key is found to be timed out**.

Of course this is not enough as there are expired keys that will never be accessed again. These keys should be expired anyway, so periodically Redis tests a few keys at random among keys with an expire set. All the keys that are already expired are deleted from the keyspace.

Specifically this is what Redis does 10 times per second:

1. Test **20 random keys** from the set of keys with an associated expire.
2. Delete all the keys found expired.
3. If more than 25% of keys were expired, start again from step 1.

This is a trivial probabilistic algorithm, basically the assumption is that our sample is representative of the whole key space, and we continue to expire until the percentage of keys that are likely to be expired is under 25%

This means that at any given moment the maximum amount of keys already expired that are using memory is at max equal to max amount of write operations per second divided by 4.



## How expires are handled in the replication link and AOF file

In order to obtain a correct behavior without sacrificing consistency, when a key expires, a [DEL](https://redis.io/commands/del) operation is synthesized in both the AOF file and gains all the attached slaves. This way the expiration process is centralized in the master instance, and there is no chance of consistency errors.

However while the slaves connected to a master will not expire keys independently (but will wait for the [DEL](https://redis.io/commands/del) coming from the master), they'll still take the full state of the expires existing in the dataset, so when a slave is elected to a master it will be able to expire the keys independently, fully acting as a master.



# References

[EXPIRE](https://redis.io/commands/expire)



---

created at 2017-09-04 18:53