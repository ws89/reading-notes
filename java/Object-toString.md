# SourceCode

> JDK8 => java.lang.Object#toString

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

> Test Output:io.github.ws89.notes.java.hashcode.HashCode@606d8acf

# Description

Returns a string representation of the object. In general, the `toString` method returns a string that "textually represents" this object. The result should be a concise but informative representation that is easy for a person to read. It is recommended that all subclasses override this ethod.

The `toString` method for class `Object` returns a string consisting of the name of the class of which the object is an instance, the at-sign character `@`', and the unsigned hexadecimal representation of the hash code of the object. In other words, this method returns a string equal to the value of:

```java
getClass().getName() + '@' + Integer.toHexString(hashCode())
```



# References

JDK 8 Source

[Object.html#toString](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#toString--)



------

created at 2017-04-26 21:16