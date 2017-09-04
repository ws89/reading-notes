# EXPIRE

- The timeout will only be cleared by commands that delete or overwrite the contents of the key, including [DEL](https://redis.io/commands/del), [SET](https://redis.io/commands/set), [GETSET](https://redis.io/commands/getset) and all the `*STORE`  commands.
- This means that all the operations that conceptually *alter* the value stored at the key without replacing it with a new one will leave the timeout untouched. For instance, incrementing the value of a key with [INCR](https://redis.io/commands/incr), pushing a new value into a list with [LPUSH](https://redis.io/commands/lpush), or altering the field value of a hash with [HSET](https://redis.io/commands/hset) are all operations that will leave the timeout untouched.
- The timeout can also be cleared, turning the key back into a persistent key, using the [PERSIST](https://redis.io/commands/persist) command.
- If a key is renamed with [RENAME](https://redis.io/commands/rename), the associated time to live is transferred to the new key name.
- ​





# References

[EXPIRE](https://redis.io/commands/expire)



---

created at 2017-09-04 18:53