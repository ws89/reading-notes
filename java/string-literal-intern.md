# intern

> java.lang.String#intern

```java
public native String intern();
```

 A pool of strings, initially empty, is maintained privately by the class `String`. 

 When the intern method is invoked, if the pool already contains a string equal to this `String` object as determined by the [`equals(Object)`](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#equals%28java.lang.Object%29) method, then the string from the pool is returned. Otherwise, this `String` object is added to the pool and a reference to this `String` object is returned. 

 It follows that for any two strings `s` and `t`, `s.intern() == t.intern()` is `true` if and only if `s.equals(t)` is `true`. 

 All literal strings and string-valued constant expressions are interned.

- Returns:

  a string that has the same contents as this string, but is guaranteed to be from a pool of unique strings.

#### Practice

```java
@Test
public void intern(){
    String s1 = "sss";
    String s2 = "sss";
    String s3 = new String("sss");
    String s4 = new String("sss");
    String s5 = s4.intern();

    System.out.println( s1 == s2 );//true
    System.out.println( s1 == s3 );//false
    System.out.println( s1 == s4 );//false
    System.out.println( s1 == s5 );//true
}
```



# literals VS. new String("string");

- `new String("text");`explicitly creates a new and referentially distinct instance of a `String` object;
- `String s = "text";` may reuse an instance from the **string constant pool** if one is available.

> [`String(String original)`](http://java.sun.com/javase/6/docs/api/java/lang/String.html#String%28java.lang.String%29) : Initializes a *newly created* `String` object so that it represents the same sequence of characters as the argument; in other words, the newly created string is a copy of the argument string. Unless an explicit copy of original is needed, **use of this constructor is unnecessary since strings are immutable**. 



**A string literal always refers to the *same* instance of class `String`**. This is because **string literals** - or, more generally, **strings that are the values of constant expressions** - **are "interned"** so as to share unique  instances, using the method `String.intern`.

- Strings computed by constant expressions are computed **at compile time** and then **treated as if they were literals**. 

  - ``` java
    String hello = "Hello";
    System.out.print((hello == ("Hel"+"lo")) + " ");//true
    ```

- Strings computed by *concatenation* **at run time** are newly created and therefore ***distinct***.

  - ``` java
    String hello = "Hello", lo = "lo";
    System.out.print((hello == ("Hel"+lo)) + " ");//false
    ```

- The result of explicitly interning a computed string is the same string as any pre-existing literal string with the same contents.

  - ``` java
    String hello = "Hello", lo = "lo";
    System.out.println(hello == ("Hel"+lo).intern());//true                
    ```

### Practice

```java
@Test
public void constructor(){
    char[] chars = {'a','b','c'};
    String s1 = "abc";
    String s2 = new String(chars);
    String s3 = s2.intern();
    System.out.println( s1 == s2 );//false
    System.out.println( s1 == s3 );//true
  
    String ss1 = "abc";
    String ss2 = "ab";
    ss2 += "c";
    System.out.println( ss1 == ss2 );//false
}
```



```java
@Test
public void concatenation(){
    String s1 = "abc";
    String s2 = "ab";
    s2 = s2 + "c";
    System.out.println( s1 == s2 );//false
}
```

```java
/* instance variables */
private String s1 = "abc";
private String s2 = "ab";
private String s3 = s2 + "c";
@Test
public void concatenation2(){
  System.out.println( s1 == s3 );//false

  final String fs1 = "abc";
  final String fs2 = "ab";
  final String fs3 = fs2 + "c";

  System.out.println( fs1 == fs3 );//true,>>Constant expression.
}
```



```java
@Test
public void concatenation3(){
    int i = 1;
    while (i<=3){
        String s1 = "not final String local variables " + i;
        String ss1 = "not final String local variables " + (i+5);

        System.out.println( s1 == s1.intern() );//true
        System.out.println( ss1 == ss1.intern() );//true

        String connection = " c ";
        String s2 = "s " + connection + i;
        String s3 = "s " + connection + i;
        System.out.println( s2 == s2.intern() );//true
        System.out.println( s3 == s3.intern() );//false ！！！！！
        System.out.println( s2 == s3.intern() );//true

       /*Like this situation,if first appeared,the concatenation string(like s1、ss1、s2 above) will be as a literal,
          otherwise it will be a plain String Object. */ 
        System.out.println();
        i++;
    }
}
```



# JMM

The area for storing string literals is in the [runtime constant pool](http://java.sun.com/docs/books/jvms/second_edition/html/Overview.doc.html#22972).  

The runtime constant pool memory area is allocated on a per-class or per-interface basis, so it's not tied to any object instances at all.

The runtime constant pool is a subset of the *method area* which "stores per-class structures such as the runtime constant pool, field and method data, and the code for methods and constructors, including the special methods used in class and instance initialization and interface type initialization".  

**When the classes are loaded in the VM, the string constants will *get copied to the heap*, to a VM-wide string pool,since equal string literals in different classes have to be the same String object.**


![](http://cdn.journaldev.com/wp-content/uploads/2012/11/String-Pool-Java1.png)

### String Pool

#### In Java 6

> In those good old days all interned strings **were stored in the PermGen**  – the fixed size part of heap mainly used for storing loaded classes and string pool. The biggest issue with such string pool in Java 6 was its location –  the PermGen. **PermGen has a fixed size and can not be expanded at runtime.** You can set it using -XX:MaxPermSize=96m option.

#### In Java 7 

> Oracle engineers made an extremely important change to the string pooling logic in Java 7 – **the string pool was relocated to the heap**. All strings are now located in the heap, as most of other ordinary objects, which allows you to manage only the heap size while tuning your application. 

> As a rule, one starts worrying about the memory consumption when the memory data set size grows to at least several hundred egabytes. In this situation, allocating 8-16 MB for a string pool with one million entries seems to be a reasonable trade off (do not use 1,000,000 as a `-XX:StringTableSize` value – it is not prime; use 1,000,003 instead).

> **You must set a higher -XX:StringTableSize value (compared to the default 1009) if you intend to actively use String.intern() – otherwise this method performance will soon degrade to a linked list performance.**

> String pool size was increased in **Java7u40 (this was a major performance update) to 60013.**Java 8 still accepts `-XX:StringTableSize` parameter and provides the comparable to Java 7 performance. The only important difference is that the default pool size was increased in **Java 8 to 60013.**

#### GC

> Yes, **all strings in the JVM string pool are eligible for garbage collection if there are no references to them from your program roots.**It **applies to all discussed versions of Java**. It means that if your interned string went out of scope and there are no other references to it – it will be garbage collected from the JVM string pool.

#### HotSpot

> In JDK 7, interned strings are no longer allocated in the permanent generation of the Java heap, but **are instead allocated in the main part of the Java heap** (known as the young and old generations), along with the other objects created by the application. This change will result in more data residing in the main Java heap, and less data in the permanent generation, and thus may require heap sizes to be adjusted. Most applications will see only relatively small differences in heap usage due to this change, but larger applications that load many classes or make heavy use of the String.intern() method will see more significant differences.

> And in Java 8 Hotspot, Permanent Generation has been completely removed.

### Summary

- Stay away from `String.intern()` method on Java 6 due to a fixed size memory area (PermGen) used for JVM string pool storage.    
- Java 7 and 8 implement the string pool in the heap memory. It means that you are limited by the whole application memory for string pooling in Java 7 and 8.    
- Use `-XX:StringTableSize` JVM parameter in Java 7 and 8 to set the string pool map size. It is fixed, because it is implemented as a hash map with lists in the buckets. Approximate the number of distinct strings in your application (which you intend to intern) and set the pool size equal to some prime number close to this value multiplied by 2 (to reduce the likelihood of collisions). It will allow `String.intern` to run in the constant time and requires a rather small memory consumption per interned string (explicitly used Java `WeakHashMap` will consume 4-5 times more memory for the same task).    
- The default value of `-XX:StringTableSize` parameter is 1009 in Java 6 and Java 7 until Java7u40. It was increased to 60013 in Java 7u40 (same value is used in Java 8 as well).   
- If you are not sure about the string pool usage, try `-XX:+PrintStringTableStatistics` JVM argument. It will print you the string pool usage when your program terminates.    




# References

[Class String](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#intern())

[What is the difference between “text” and new String(“text”)?](http://stackoverflow.com/questions/3052442/what-is-the-difference-between-text-and-new-stringtext)

[Strings are objects in Java, so why don't we use 'new' to create them?](http://stackoverflow.com/questions/2009228/strings-are-objects-in-java-so-why-dont-we-use-new-to-create-them)

[How do I compare strings in Java?](http://stackoverflow.com/questions/513832/how-do-i-compare-strings-in-java)

[String Constant Pool](https://community.oracle.com/message/7318288#7318288)

[String Literal vs String Object](http://stackoverflow.com/questions/40188409/string-literal-vs-string-object)

[3.10.5. String Literals](http://docs.oracle.com/javase/specs/jls/se7/html/jls-3.html#jls-3.10.5)

[What is the Java string pool and how is “s” different from new String(“s”)?](http://stackoverflow.com/questions/2486191/what-is-the-java-string-pool-and-how-is-s-different-from-new-strings)

[What is Java String Pool?](http://www.journaldev.com/797/what-is-java-string-pool)

[Where does Java's String constant pool live, the heap or the stack?](http://stackoverflow.com/questions/4918399/where-does-javas-string-constant-pool-live-the-heap-or-the-stack)

[String.intern in Java 6, 7 and 8 – string pooling](http://java-performance.info/string-intern-in-java-6-7-8/)



---

created at 2017-04-28 15:52

