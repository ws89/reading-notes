# binary safe string

**Binary safe string does not make any assumption about its length or terminating character.** For example, in Redis (since you have mentioned Redis in your question), syntax for a binary safe bulk string is "$str_len\r\nbinary_safe_string\r\n". Here, the length of the string is made part of the string representation. With this, you can save anything as a string, say a JPEG image.Refer to [Redis documentation](https://redis.io/topics/protocol) for more details.



## Redis keys

Redis keys are binary safe, this means that you can use any binary sequence as a key, from a string like "foo" to the content of a JPEG file.The empty string is also a valid key.

A few other rules about keys:

- Very long keys are not a good idea. For instance a key of 1024 bytes is a bad idea not only memory-wise, but also because the lookup of the key in the dataset may require several costly key-comparisons. Even when the task at hand is to match the existence of a large value, hashing it (for example with SHA1) is a better idea, especially from the perspective of memory and bandwidth.
- Very short keys are often not a good idea. There is little point in writing  "u1000flw" as a key if you can instead write "user:1000:followers".  The latter is more readable and the added space is minor compared to the space used by            the key object itself and the value object. While short keys will obviously consume a bit less memory, your job is to find the right balance.
- Try to stick with a schema. For instance "object-type:id" is a good idea, as in "user:1000". Dots or dashes are often used for multi-word            fields, as in "comment:1234:reply.to" or "comment:1234:reply-to".
- The maximum allowed key size is 512 MB.





## References

[What is a binary safe string?](https://stackoverflow.com/questions/44377344/what-is-a-binary-safe-string)



---

created at 2017-07-08 23:14