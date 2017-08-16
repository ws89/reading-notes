# Generations

![http://www.pointsoftware.ch/wp-content/uploads/2012/10/JUtH_20121024_RuntimeDataAreas_1_MemoryModel.png](http://www.pointsoftware.ch/wp-content/uploads/2012/10/JUtH_20121024_RuntimeDataAreas_1_MemoryModel.png)

- **Young Generation**: is where all new objects are allocated and aged.
- **Eden Space (heap)**: The pool from which memory is initially allocated for most objects.
- **Survivor Space (heap)**: The pool containing objects that have survived the garbage collection of the Eden space.
- **Tenured Generation (heap)**: The pool containing objects that have existed for some time in the survivor space.
- **Permanent Generation (non-heap)**: The pool containing all the reflective data of the virtual machine itself, such as class and method objects. With Java VMs that use class data sharing, this generation is divided into read-only and read-write areas.

**Note** : the **permanent generation** is ***not part of the heap***. It's a separate space for class definitions and related data, as well as where interned strings live.



This is how Heap Structure look like in Java 6

![img](/attachments/java/jvm/java_memory_permGen.png)



# Permanent Generation

The pool containing all the reflective data of the virtual machine itself, such as class and method objects. With Java VMs that use class data sharing, this generation is divided into read-only and read-write areas.

The Permanent generation contains metadata required by the JVM to describe the classes and methods used in the application. The permanent generation is populated by the JVM at runtime based on classes in  use by the application. In addition, Java SE library classes and methods may be stored here.

Classes may get collected (unloaded) if the JVM finds they are no longer needed and space may be needed for other classes. The permanent generation is included in a **full garbage collection**.

- Region of Java Heap for JVM Class Metadata.
- Hotspot’s internal representation of Java Classes.
- Class hierarchy information, fields, names
- Method compilation information and bytecodes
- Variables
- Constant pool and symbolic resolution



## PermGen Size

- Limited to MaxPermSize – default ~64M - 85M

- Contiguous with Java Heap : Identifying young references from old gen and permgen would  be more expensive and complicated with a non-contiguous heap – card table(A kind of remembered set that records where oops have changed in a generation).

- Once exhausted throws OutOfMemoryError "PermGen space".

- - Application could clear references to cause class unloading.
  - Restart with larger MaxPermSize.

- Size needed depends on number of classes, size of methods, size of constant pools.



## Why was PermGen Eliminated?

- Fixed size at startup – difficult to tune.

- - -XX:MaxPermSize=?

- Internal Hotspot types were Java objects : Could move with full GC, opaque, not strongly typed and hard to debug, needed meta-metadata.

- Simplify full collections : Special iterators for metadata for each collector

- Want to deallocate class data concurrently and not during GC pause

- Enable future improvements that were limited by PermGen.





# Metaspace

![](/attachments/java/jvm/jvm_metapsace.png)



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

[Metaspace in Java 8](http://java-latte.blogspot.in/2014/03/metaspace-in-java-8.html)

[Method Area and PermGen](https://stackoverflow.com/questions/9095748/method-area-and-permgen)

[2.5.4. Method Area](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.4)

---

created at 2017-08-14 23:00