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



# References

[Class String](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#intern())



---

created at 2017-04-28 15:52

