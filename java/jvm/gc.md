# JRE (Java Runtime Environment)

The JRE consists of the Java Virtual Machine (JVM), Java platform core classes, and supporting Java platform libraries.



# Java Virtual Machine

The Java Virtual Machine (JVM) is an abstract computing machine. The JVM is a program that looks like a machine to the programs written to execute in it. Each JVM implementation for a specific operating system, translates the Java programming instructions into instructions and commands that run on the local operating system. This way, Java programs achieve platform independence.

The Java virtual machine knows nothing of the Java programming language, only of a particular binary format, the class file format. A class file contains Java virtual machine instructions (or bytecodes) and a symbol table, as well as other ancillary information.

For the sake of security, the Java virtual machine imposes strong syntactic and structural constraints on the code in a class file. However, any language with functionality that can be expressed in terms of a valid class file can be hosted by the Java virtual machine. Attracted by a generally available, machine-independent platform, implementors of other languages can turn to the Java virtual machine as a delivery vehicle for their languages. 



# JVM Architecture

## Hotspot Architecture

The HotSpot JVM possesses an architecture that supports a strong foundation of features and capabilities and supports the ability to realize high performance and massive scalability. For example, the HotSpot JVM **JIT compilers generate dynamic optimizations**. In other words, they make optimization decisions while the Java application is running and generate high-performing native machine instructions targeted for the underlying system architecture. In addition, through the maturing evolution and continuous engineering of its runtime environment and **multithreaded garbage collector**, the HotSpot JVM yields high scalability on even the largest available computer systems.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide1.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide1.png)

The main components of the JVM include the **classloader**, the **runtime data areas**, and **the execution engine**.

#### Key Hotspot Components

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide2.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide2.png)

There are three components of the JVM that are focused on when **tuning performance**. The *heap* is where your object data is stored. This area is then managed by the garbage collector selected at startup. Most tuning options relate to **sizing the heap and choosing the most appropriate garbage collector** for your situation. The **JIT compiler** also has a big impact on performance but rarely requires tuning with the newer versions of the JVM.



## Performance Basics

Typically, when tuning a Java application, the focus is on one of two main goals: **responsiveness or throughput**. 



#### Responsiveness

Responsiveness refers to how quickly an application or system responds with a requested piece of data. Examples include: 

- How quickly a desktop UI responds to an event
- How fast a website returns a page
- How fast a database query is returned

For applications that focus on responsiveness, large pause times are not acceptable. **The focus is on responding in short periods of time**.



#### Throughput

Throughput focuses on **maximizing the amount of work by an application in a specific period of time**. Examples of how throughput might be measured include:

- The number of transactions completed in a given time.
- The number of jobs that a batch program can complete in an hour.
- The number of database queries that can be completed in an hour.

High pause times are acceptable for applications that focus on throughput. Since **high throughput applications focus on benchmarks over  longer periods of time, quick response time is not a consideration**.





# GC (Garbage Collection)

Automatic garbage collection is the process of looking at heap memory, identifying which objects are in use and which are not, and deleting the unused objects. An in use object, or a referenced object, means that some part of your program still maintains a pointer to that object. An unused object, or unreferenced object, is no longer referenced by any part of your program. So the memory used by an unreferenced object can be reclaimed.



## Marking

The first step in the process is called marking. This is where the garbage collector identifies which pieces of memory are in use and which are not.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide3.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide3.png)



## Normal Deletion

Normal deletion removes unreferenced objects leaving referenced objects and pointers to free space.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide1b.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide1b.png)





The memory allocator holds references to blocks of free space where new object can be allocated.



### Deletion with Compacting

To further improve performance, in addition to deleting unreferenced objects, you can also compact the remaining referenced objects. By moving referenced object together, this makes new memory allocation much easier and faster.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide4.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide4.png)



# Generational GC

As stated earlier, having to mark and compact all the objects in a JVM is inefficient. As more and more objects are allocated, the list of objects grows and grows leading to longer and longer garbage collection time. However, empirical analysis of applications has shown that **most objects are short lived**.



Here is an example of such data. The Y axis shows the number of bytes allocated and the X access shows the number of bytes allocated over time.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/ObjectLifetime.gif](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/ObjectLifetime.gif)

As you can see, fewer and fewer objects remain allocated over time. In fact most objects have a very short life as shown by the higher values on the left side of the graph.



## JVM Generations

The heap parts are: Young Generation, Old or Tenured Generation, and Permanent Generation.

![http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide5.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide5.png)

The **Young Generation** is where all new objects are allocated and aged. When the young generation fills up, this causes a **minor garbage collection**. Minor collections can be optimized assuming a high object mortality rate. A young generation full of dead objects is collected very quickly. Some surviving objects are aged and eventually move to the old generation. 

**Stop the World Event** - All minor garbage collections are "Stop the World" events. This means that all application threads are stopped until the operation completes. Minor garbage collections are *always* Stop the World events.



The **Old Generation** is used to store long surviving objects. Typically, a threshold is set for young generation object and when that age is met, the object gets moved to the old generation. Eventually the old generation needs to be collected.  This event is called a **major garbage collection**. 

Major garbage collection are also **Stop the World events**. Often a major collection is much slower because it involves all live objects. So for Responsive applications, major garbage collections should be minimized. Also note, that the length of the Stop the World event for a major garbage collection is affected by the kind of garbage collector that is used for the old generation space.



The **Permanent generation** contains metadata required by the JVM to describe the classes and methods used in the application. The permanent generation is populated by the JVM at runtime based on classes in use by the application. In addition, Java SE library classes and methods may be stored here.

Classes may get collected (unloaded) if the JVM finds they are no longer needed and space may be needed for other classes. The permanent generation is included in a **full garbage collection**.



## GC Process

1.First, any new objects are allocated to the eden space. Both survivor spaces start out empty.

![images/gcslides/Slide13.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide13.png)

2.When the eden space fills up, a minor garbage collection is **triggered**.

![images/gcslides/Slide14.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide14.png)

3.Referenced objects are moved to the first survivor space. Unreferenced objects are deleted when the eden space is cleared.

![images/gcslides/Slide6.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide6.png)





4.At the next minor GC, the same thing happens for the eden space. Unreferenced objects are deleted and referenced objects are moved to a survivor space. However, in this case, they are moved to the second survivor space (S1). In addition, objects from the last minor GC on the first survivor space (S0) have their age incremented and get moved to S1. Once all surviving objects have been moved to S1, both S0 and eden 
are cleared. Notice we now have differently aged object in the survivor space.

![images/gcslides/Slide8.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide8.png)

5.At the next minor GC, the same process repeats. However this time the survivor spaces switch. Referenced objects are moved to S0. Surviving objects are aged. Eden and S1 are cleared.

![images/gcslides/Slide9.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide9.png)





6.This slide demonstrates promotion. After a minor GC, when aged objects reach a certain age threshold (8 in this example) they are promoted from young generation to old generation.

![images/gcslides/Slide7.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide7.png)



7.As minor GCs continue to occure objects will continue to be promoted to the old generation space.

![images/gcslides/Slide10.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide10.png)



8.So that pretty much **covers the entire process with the young generation**. Eventually, **a major GC will be performed** on the old generation which cleans up and compacts that space.

![images/gcslides/Slide11.png](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide11.png)







# PermGen VS. Metaspace

>  **Metaspace by default auto increases** its size (up to what the underlying OS provides), while PermGen always has a fixed maximum size. You can set a fixed maximum for Metaspace with JVM parameters, but you cannot make PermGen auto increase.

> In short, Metaspace size **auto increases in native memory** as required to load class metadata if not restricted with `-XX:MaxMetaspaceSize`



# References

[Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)

[What is the difference between PermGen and Metaspace?](https://stackoverflow.com/questions/27131165/what-is-the-difference-between-permgen-and-metaspace)

[2.5.4. Method Area](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.4)

[What triggers a full garbage collection in Java?](https://stackoverflow.com/questions/9546392/what-triggers-a-full-garbage-collection-in-java)

---

created at 2017-08-11 23:37