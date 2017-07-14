# Transactions

[MULTI](https://redis.io/commands/multi), [EXEC](https://redis.io/commands/exec), [DISCARD](https://redis.io/commands/discard) and [WATCH](https://redis.io/commands/watch) are the foundation of transactions in Redis.  They allow the execution of a group of commands in a single step, with two important guarantees:

- **All the commands in a transaction are serialized and executed sequentially.** It can never happen that a request issued by another client is served **in the middle** of the execution of a Redis transaction. This guarantees that the commands are executed as a single isolated operation.
- **Either all of the commands or none are processed, so a Redis transaction is also atomic**. The [EXEC](https://redis.io/commands/exec) command triggers the execution of all the commands in the transaction, so if a client loses the connection to the server in the context of a transaction before calling the [MULTI](https://redis.io/commands/multi) command none of the operations are performed, instead if the [EXEC](https://redis.io/commands/exec) command is called, all the operations are performed. When using the [append-only file](https://redis.io/topics/persistence#append-only-file) Redis makes sure to use a single write(2) syscall to write the transaction on disk. However if the Redis server crashes or is killed by the system administrator in some hard way ***it is possible that only a partial number of operations are registered***. Redis will detect this condition at restart, and will exit with an error. Using the `redis-check-aof` tool it is possible to fix the append only file that will remove the partial transaction so that the server can start again.



**Practice:**

```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr t1
QUEUED
127.0.0.1:6379> incr t2
QUEUED
127.0.0.1:6379> exec
1) (error) WRONGTYPE Operation against a key holding the wrong kind of value
2) (integer) 3
127.0.0.1:6379>
```



## Errors inside a transaction

During a transaction it is possible to encounter two kind of command errors:

- A command may fail to be queued, so there may be an error before [EXEC](https://redis.io/commands/exec) is called. For instance the command **may be syntactically wrong** (wrong number of arguments, wrong command name, ...), or there may be some critical condition like an out of memory condition (if the server is configured to have a memory limit using the `maxmemory` directive).
- A command may fail *after* [EXEC](https://redis.io/commands/exec) is called, for instance since we **performed an operation against a key with the wrong value** (like calling a list operation against a string value).

However starting with Redis 2.6.5, the server will remember that there was an error during the accumulation of commands, and will **refuse to  execute the transaction** returning also an error during [EXEC](https://redis.io/commands/exec), and **discarding the transaction automatically**.

Errors happening *after* [EXEC](https://redis.io/commands/exec) instead are not handled in a special way: **all the other commands will be executed even if some command fails during the transaction**.

It's important to note that **even when a command fails, all the other commands in the queue are processed** – Redis will *not* stop the processing of commands.



## Why Redis does not support roll backs?

If you have a relational databases background, the fact that Redis commands can fail during a transaction, but still Redis will execute the rest of the transaction instead of rolling back, may look odd to you.

However there are good opinions for this behavior:

- Redis commands can fail only if called with a wrong syntax (and the problem is not detectable during the command queueing), or against keys holding the wrong data type: this means that in practical terms a failing command is the result of a programming errors, and **a kind of error that is very likely to be detected during development, and not in production**.
- Redis is internally **simplified and faster** because it does not need the ability to roll back.



## Optimistic locking using check-and-set

[WATCH](https://redis.io/commands/watch) is used to provide a check-and-set (CAS) behavior to Redis  transactions.

`WATCH`ed keys are monitored in order to detect changes against them. If  at least one watched key is modified before the [EXEC](https://redis.io/commands/exec) command, the  whole transaction aborts, and [EXEC](https://redis.io/commands/exec) returns a [Null reply](https://redis.io/topics/protocol#nil-reply) to notify that  the transaction failed.

This will work reliably only if we have a single client performing the operation in a given time. If multiple clients try to increment the key at about the same time there will be a race condition. For instance, client A and B will read the old value, for instance, 10. The value will be incremented to 11 by both the clients, and finally [SET](https://redis.io/commands/set) as the value of the key. So the final value will be 11 instead of 12.

```
127.0.0.1:6379> get t2
"1"
127.0.0.1:6379> watch t2
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set t2 2
QUEUED
127.0.0.1:6379> exec
(nil)
127.0.0.1:6379>
127.0.0.1:6379> get t2
"0"
127.0.0.1:6379>
```

Using the above code, if there are race conditions and another client modifies the result of `val` in the time between our call to [WATCH](https://redis.io/commands/watch) and          our call to [EXEC](https://redis.io/commands/exec), the transaction will fail.

We just have to repeat the operation hoping this time we'll not get a new race. This form of locking is called *optimistic locking* and is a very powerful form of locking. 

> NOTE:watch support ABA by practice.



## [WATCH](https://redis.io/commands/watch) explained

So what is [WATCH](https://redis.io/commands/watch) really about? It is a command that will make the [EXEC](https://redis.io/commands/exec) conditional: we are asking Redis to perform the transaction only if none of the `WATCH`ed keys were modified. (But they might be changed by the same client inside the transaction without aborting it. )Otherwise the transaction is not entered at all.

```
127.0.0.1:6379> get t2
"0"
127.0.0.1:6379> watch t2
OK
127.0.0.1:6379> set t2 1
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set t2 1
QUEUED
127.0.0.1:6379> exec
(nil)
127.0.0.1:6379>

```



Note that if you [WATCH](https://redis.io/commands/watch) a volatile key and Redis expires the key after you `WATCH`ed it, [EXEC](https://redis.io/commands/exec) will still work. 

```
127.0.0.1:6379> set t3 3 EX 50
OK
127.0.0.1:6379> pttl t3
(integer) 42696
127.0.0.1:6379> watch t3
OK
127.0.0.1:6379> set t3 2 EX 5   //!!!!!!!!!!!!!!!!!!
OK
127.0.0.1:6379> pttl t3
(integer) -2
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set t3 1
QUEUED
127.0.0.1:6379> exec
(nil)						//!!!!!!!!!!!!!!!!!!  fail!
127.0.0.1:6379>
```



```
127.0.0.1:6379> set t3 3 EX 20
OK
127.0.0.1:6379> pttl t3
(integer) 16285
127.0.0.1:6379> watch t3
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set t3 1
QUEUED
127.0.0.1:6379> exec
1) OK						//!!!!!!!!!!!!!!!!!! success!
127.0.0.1:6379> ttl t3
(integer) -1
127.0.0.1:6379> get t3
"1"
```



Simply all the [WATCH](https://redis.io/commands/watch) calls will have the effects to watch for changes starting from the call, up to the moment [EXEC](https://redis.io/commands/exec) is called. You can also send any number of keys to a single [WATCH](https://redis.io/commands/watch) call.



When [EXEC](https://redis.io/commands/exec) is called, all keys are `UNWATCH`ed, regardless of whether the transaction was aborted or not.  Also when a client connection is closed, everything gets `UNWATCH`ed.



It is also possible to use the [UNWATCH](https://redis.io/commands/unwatch) command (without arguments)  in order to flush all the watched keys. Sometimes this is useful as we optimistically lock a few keys, since possibly we need to perform a transaction to alter those keys, but after reading the current content of the keys we don't want to proceed.  When this happens we just call  [UNWATCH](https://redis.io/commands/unwatch) so that the connection can already be used freely for new transactions.



### Using [WATCH](https://redis.io/commands/watch) to implement ZPOP

A good example to illustrate how [WATCH](https://redis.io/commands/watch) can be used to create new atomic operations otherwise not supported by Redis is to implement ZPOP, that is a command that pops the element with the lower score from a sorted set in an atomic way. This is the simplest implementation:

```
WATCH zset
element = ZRANGE zset 0 0
MULTI
ZREM zset element
EXEC
```



# References

[Transactions](https://redis.io/topics/transactions#transactions)

---

created at 2017-07-14 19:38