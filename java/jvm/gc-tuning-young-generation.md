# Young Generation

Most objects “die” young, i.e., after their creation they are not referenced for long in the program flow. Also, it has been observed that young objects are rarely referenced by older objects.

The Sun/Oracle HotSpot JVM further divides the young generation into three sub-areas: **one large** area named “Eden” and **two smaller** “survivor spaces” named “From” and “To”. As a rule, new objects are allocated in “Eden” (with the exception that **if a new object is too large to fit into “Eden” space, it will be** 
**directly allocated in the *old generation***).

Based on the assumption that most of the young objects may be deleted during a GC, a copying strategy (“copy collection”) is being used for young generation GC. At the beginning of a GC, the survivor space “To” is empty and objects can only exist in “Eden” or “From”. Then, during the GC, all objects in “Eden” that are still being referenced are moved into “To”. Regarding “From”, the still referenced objects in this space are handled depending on their age. If they have not reached a certain age (“tenuring threshold”), they are also moved into “To”. Otherwise they are moved into the old generation. At the end of this copying procedure, “Eden” and “From” can be considered empty (because they only contain dead objects), and all live objects in the young generation are located in “To”. Should “To” fill up at some point during the GC, all remaining objects are moved into the old generation instead (and will never return). As a final step, “From” and “To” swap their roles (or, more precisely, their names) so that “To” is empty again for the next GC and “From” contains all remaining young objects.

![https://blog.codecentric.de/files/2011/08/young_gc.png](https://blog.codecentric.de/files/2011/08/young_gc.png)

Example showing the initial state and the result of a young generation GC. Free space is green, objects not referenced anymore are yellow, and still referenced objects are red. In this example, the survivor spaces are large enough so that no objects need to be moved into the old generation.



It now becomes clear why young generation sizing is so important: ***If the young generation is too small, short-lived objects will quickly be moved into the old generation where they are harder to collect. Conversely, if the young generation is too large, we will have lots of unnecessary copying for long-lived objects that will later be moved to the old generation anyway.*** Thus we need to find a compromise somewhere between small and large young generation size. Unfortunately, finding the right compromise for a particular application can often only be done by systematic measurement and tuning. And that’s where the JVM flags come into play.



## -XX:NewSize and -XX:MaxNewSize

Similar to the total heap size (with `-Xms` and `-Xmx`) it is possible to explicitly set a lower and upper bound for the size of the young generation. However, when setting `-XX:MaxNewSize` we need to take into account that the young generation is only one part of the heap and that the larger we choose its size the smaller the old generation will be. For stability reasons it is not allowed to choose a young generation size larger than the old generation, because in the worst case it may become necessary for a GC to move all objects from the young generation into the old generation. Thus `-Xmx/2` is an upper bound for `-XX:MaxNewSize`.

For performance reasons we may also specify the initial size of the young generation using the flag `-XX:NewSize`. This is useful if we know the rate at which young objects are being allocated (for example because we measured it!) and can save some of the costs required for slowly growing the young generation to that size over time.



## -XX:NewRatio

It is also possible to specify the young generation size in relation to the size of the old generation. The potential advantage of this approach is that the young generation will grow and shrink automatically when the JVM dynamically adjusts the total heap size at run time. The flag `-XX:NewRatio` allows us to specify the factor by which the old generation should be larger than the young generation. For example, with `-XX:NewRatio=3` the old generation will be three times as large as the young generation. That is, the old generation will occupy 3/4 and the young generation will occupy 1/4 of the heap.

**If we mix absolute and relative sizing of the young generation, the absolute values always have precedence**. Consider the following example:

```java
$ java -XX:NewSize=32m -XX:MaxNewSize=512m -XX:NewRatio=3 MyApp
```

With these settings, the JVM will try to size the young generation at one third of the old generation size, but it will never let young generation size fall below 32 MB or exceed 512 MB.

There is no general rule if absolute or relative young generation sizing is preferable. If we know the memory usage of our application well, it can be advantageous to specify a fixed size both for the total heap and the young generation, and it can also be useful to specify a ratio. If we only know a little or maybe nothing at all about our application in this respect, the correct approach is to just let the JVM do the work and not to mess around with the flags. If the application runs smoothly, we can be happy that we didn’t put in extra effort where none was needed. And should we encounter performance problems or OutOfMemoryErrors, we would still need to first perform a series of meaningful measurements to narrow down the root cause of the problem before moving on to tuning.



## -XX:SurvivorRatio

The flag `-XX:SurvivorRatio` is similar to `-XX:NewRatio` but applies to the areas inside the young generation. The value of `-XX:SurvivorRatio` specifies how large “Eden” should be sized relative to one of the two survivor spaces. For example, with `-XX:SurvivorRatio=10` we dimension “Eden” ten times as large as “To” (and at the same time ten times as large as “From”). As a result, “Eden” occupies 10/12 of the young generation while “To” and “From” each occupy 1/12. Note that the two survivor spaces are always equal in size.

What effect does survivor space sizing have? Suppose that the survivor spaces are very small compared to “Eden”. Then we have lots of space in “Eden” for newly allocated objects, which is desirable. If all these objects can be collected during the next GC, “Eden” is empty again and everything is fine. However, if some of these young objects are still being referenced, we have only little space in the survivor spaces to accommodate them. As a consequence, most of these objects will be moved to the old generation right after their first GC, which is not desirable. Now let us consider the opposite situation: Suppose that the survivor spaces are relatively large in size. Then they have lots of space to fulfill their main purpose, to accommodate objects that survive one or more GCs but still die young. However, the smaller “Eden” space will be exhausted more quickly, which increases the number of young generation GCs performed. This is undesirable.

In summary, we want to minimize the number of short-lived objects that are prematurely moved into the old generation, but we also want to minimize the number and duration of young generation GCs. Once again we need to find a compromise, which in turn depends on the characteristics of the application at hand. A good starting point for finding an adequate compromise is to learn about the age distribution of the objects in the particular application.



## -XX:+PrintTenuringDistribution

With the flag `-XX:+PrintTenuringDistribution` we tell the JVM to print the age distribution of all objects contained in the survivor spaces on each young generation GC. Take the following example:

```
Desired survivor size 75497472 bytes, new threshold 15 (max 15)
- age   1:   19321624 bytes,   19321624 total
- age   2:      79376 bytes,   19401000 total
- age   3:    2904256 bytes,   22305256 total
```

The first line tells us that the target utilization of the “To” survivor space is about 75 MB. It also shows some information about the “tenuring threshold”, which represents the number of GCs that an object may stay in the young generation before it is moved into the old generation (i.e., the maximum age of the object before it gets promoted). In this example, we see that the current tenuring threshold is 15 and that its maximum value is 15 as well.

The next lines show, for each object age lower than the tenuring threshold, the total number of bytes of all objects that currently have that age (if no objects currently exist for a certain age, that line is omitted). In the example, about 19 MB have already survived one GC, about 79 KB have survived two GCs, and about 3 MB have survived three GCs. At the end of each line, we see the accumulated byte count of all objects up to that age. Thus, the “total” value in the last line indicates that the “To” survivor space currently contains about 22 MB of object data. As the target utilization of “To” is 75 MB and the current tenuring threshold is 15, we can conclude that no objects have to be promoted to the old generation as part of the current young generation GC. Now suppose that the next GC leads to the following output:

```
Desired survivor size 75497472 bytes, new threshold 2 (max 15)
- age   1:   68407384 bytes,   68407384 total
- age   2:   12494576 bytes,   80901960 total
- age   3:      79376 bytes,   80981336 total
- age   4:    2904256 bytes,   83885592 total
```

Let us compare the output to the previous tenuring distribution. Apparently, all the objects of age 2 and 3 from the previous output are still located in “To”, because here we see exactly the same number of bytes printed for age 3 and 4. We can also conclude that some of the objects in “To” have been successfully collected by the GC, because now we only have 12 MB of objects of age 2 while in the previous output we had 19 MB listed for age 1. Finally, we see that about 68 MB of new objects, shown at age 1, have been moved from “Eden” into “To” during the last GC.

Note that the total number of bytes in “To” – in this case almost 84 MB – is now larger than the desired number of 75 MB. As a consequence, the JVM has reduced the tenuring threshold from 15 to 2, so that with the next GC some of the objects will be forced to leave “To”. These objects will then either be collected (if they have died in the meantime) or moved to the old generation (if they are still referenced).





## -XX:InitialTenuringThreshold, -XX:MaxTenuringThreshold and -XX:TargetSurvivorRatio

The tuning knobs shown in the output of `-XX:+PrintTenuringDistribution` can be adjusted by various flags. With `-XX:InitialTenuringThreshold` and `-XX:MaxTenuringThreshold` we can set the initial and maximum value of the tenuring threshold, respectively. Additionally, we can use `-XX:TargetSurvivorRatio` to specify the target utilization (in percent) of “To” at the end of a young generation GC. For example, the combination `-XX:MaxTenuringThreshold=10 -XX:TargetSurvivorRatio=90` sets an upper bound of 10 for the tenuring threshold and a target utilization of 90 percent for the “To” survivor space.

While there are different approaches to use these flags to tune young generation behavior, no general guideline is available. We restrict ourselves to two cases that are pretty clear:

- If the tenuring distribution shows that many objects just grow older and older before finally reaching the maximum tenuring threshold, this indicates that the value of `-XX:MaxTenuringThreshold` may be too large.
- If the value of `-XX:MaxTenuringThreshold` is larger than 1 but most objects never reach an age larger than 1, we should take a look at the target utilization of “To”. Should the target utilization never be reached, then we know that all young objects get collected by the GC, which is exactly what we want. However, if the target utilization is frequently reached, then at least some of the objects beyond age 1 have been moved into the old generation, and maybe prematurely so. In this case, we can try to tune the survivor spaces by increasing their size or target utilization.

## -XX:+NeverTenure and -XX:+AlwaysTenure

 Finally, I would like to quickly mention two rather exotic flags which we can use to test two extremes of young generation GC behavior. If `-XX:+NeverTenure` is set, objects are never promoted to the old generation. This behavior makes sense when we are sure that we don’t need an old generation at all. However, as such, the flag is apparently very risky and also wastes at least half of the reserved heap memory. The inverse behavior can be triggered with `-XX:+AlwaysTenure`, i.e., no survivor spaces are used so that all young objects are immediately promoted to the old generation on their first GC. Again, it is difficult to find a valid use case for this flag – it can be fun to see what happens in a testing environment, but apart from that I would not recommend using either flag.



# References

[Useful JVM Flags – Part 5 (Young Generation Garbage Collection)](https://blog.codecentric.de/en/2012/08/useful-jvm-flags-part-5-young-generation-garbage-collection/)



---

created at 2017-08-19 01:00