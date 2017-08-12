# JRE (Java Runtime Environment)

The JRE consists of the Java Virtual Machine (JVM), Java platform core classes, and supporting Java platform libraries.





# PermGen VS. Metaspace

>  **Metaspace by default auto increases** its size (up to what the underlying OS provides), while PermGen always has a fixed maximum size. You can set a fixed maximum for Metaspace with JVM parameters, but you cannot make PermGen auto increase.

> In short, Metaspace size **auto increases in native memory** as required to load class metadata if not restricted with `-XX:MaxMetaspaceSize`



# References

[Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)

[What is the difference between PermGen and Metaspace?](https://stackoverflow.com/questions/27131165/what-is-the-difference-between-permgen-and-metaspace)

---

created at 2017-08-11 23:37