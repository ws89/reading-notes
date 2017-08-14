# Metaspace

## Metaspace Details

- Most allocations for the class metadata previously done out of the permanent generation are now allocated out of **native memory**. Some miscellaneous  data has been moved to the Java heap.
- The permanent generation has been removed. The  **PermSize and  MaxPermSize are ignored** and a warning is issued if they are present on the command line.
- The klasses that were used to described class  metadata have been  removed (klassKlass and it's derivatives).
- By default class metadata allocation is only limited by the  amount of available native memory. Use the new flag **MaxMetaspaceSize** to limit the amount of  native memory used for class metadata. It is analogous to MaxPermSize.
- In 64bit VM when compressed oops are used a special fixed size  space is used for classes to set a compressed class pointer in object's header. The size  of that class's space is controlled by ClassMetaspaceSize flag with default value 2Mbytes.
- A garbage collection may be induced to collect dead classloaders  and classes. The first  garbage collection will be induced when the class metadata  usage reaches MetaspaceSize (12Mbytes on the 32bit client VM and 16Mbytes on the 32bit  server VM with larger sizes on the 64bit VM's). Set MetaspaceSize to a higher value to delay the induced garbage collections. After an induced garbage collection the class metadata usage needed to induce the next  garbage collection may be  increased.
- Whereas class metadata was previously garbage collected as part of the permanent generation, without the permanent generation the storage for class metadata is explicitly managed. C-heap allocation is not used.


---

**PermGen space situation**        

- This memory space is completely removed.
- The PermSize and MaxPermSize JVM arguments are ignored and a warning is issued if present at start-up.



**Metaspace memory allocation model**         

- Most allocations for the class metadata are now allocated out of native memory.



**Metaspace capacity**          

- By default class metadata allocation is limited by the amount of available native memory (capacity will of course depend if you use a 32-bit JVM vs. 64-bit along with OS virtual memory availability).
- A new flag is available (MaxMetaspaceSize), allowing you to limit the amount of native memory used for class metadata. If you don’t specify this flag, the Metaspace will dynamically re-size depending of the application demand at runtime.



**Metaspace garbage collection**            

- Garbage collection of the dead classes and classloaders is triggered once the class metadata usage reaches the “MaxMetaspaceSize”.
- Proper monitoring & tuning of the Metaspace will obviously be required in order to limit the frequency or delay of such garbage collections. Excessive Metaspace garbage collections may be a symptom of classes, classloaders memory leak or inadequate sizing for your application.



**Java heap space impact**              

- Some miscellaneous data has been moved to the Java heap space. This means you may observe an increase of the Java heap space following a future JDK 8 upgrade.



# PermGen VS. Metaspace

> **Metaspace by default auto increases** its size (up to what the underlying OS provides), while PermGen always has a fixed maximum size. You can set a fixed maximum for Metaspace with JVM parameters, but you cannot make PermGen auto increase.

> In short, Metaspace size **auto increases in native memory** as required to load class metadata if not restricted with `-XX:MaxMetaspaceSize`



# References

[PermGen Elimination project is promoting](http://mail.openjdk.java.net/pipermail/hotspot-dev/2012-September/006679.html)

[Java 8: From PermGen to Metaspace](https://dzone.com/articles/java-8-permgen-metaspace)

[What is the difference between PermGen and Metaspace?](https://stackoverflow.com/questions/27131165/what-is-the-difference-between-permgen-and-metaspace)

---

created at 2017-08-14 23:00