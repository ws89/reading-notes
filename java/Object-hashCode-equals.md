# java.lang.Object#hashCode

### SourceCode

```java
public native int hashCode();
```

### Description

Returns a hash code value for the object. This method is supported for the benefit of hash tables such as those provided by [`HashMap`](https://docs.oracle.com/javase/7/docs/api/java/util/HashMap.html).

The general contract of `hashCode` is:

- Whenever it is invoked on the same object more than once during an execution of a Java application, the `hashCode` method must consistently return the same integer, provided no information used in `equals` comparisons on the object is modified*`(只要没有修改在这个Object的equals方法中使用的属性)`*.This integer need not remain consistent from one execution of an application to another execution of the same application.
- **If two objects are equal according to the `equals(Object)` method, then calling the `hashCode` method on each of the two objects must produce the same integer result.**
- It is *not* required that if two objects are unequal according to the `equals(java.lang.Object)` method, then calling the `hashCode` method on each of the two objects must produce distinct integer results.  However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.



# java.lang.System#identityHashCode

### SourceCode & Description

```java
public static native int identityHashCode(Object x);
```

Returns the same hash code for the given object as would be returned by the **default method hashCode()**, whether or not the given object's class overrides hashCode().

 The hash code for the null reference is zero.

### Practice

> override hashCode() methd

```java
public class HashCode {
    private Integer i;
  
    @Override
    public int hashCode() {
        int result = i != null ? i.hashCode() : 0;
        result = 31 * result + (l != null ? l.hashCode() : 0);
        return result;
    }
  
    @Test
    public void test(){
        HashCode hashCode1 = new HashCode();
        System.out.println( hashCode1.hashCode() + "\t" + System.identityHashCode(hashCode1));
      	//output:0	1522311648
    }
}
```

> not override hashCode() method

``` java
public class HashCode {
    private Integer i;
  
    @Test
    public void test(){
        HashCode hashCode1 = new HashCode();
        System.out.println( hashCode1.hashCode() + "\t" + System.identityHashCode(hashCode1));
      	//output:1522311648	1522311648
    }
}
```



# java.lang.Object#equals

### SourceCode & Description

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

 The `equals` method implements an equivalence relation on non-null object references: 

- It is ***reflexive***: for any non-null reference value x`, `x.equals(x)` should return     `true`. 
- It is ***symmetric***: for any non-null reference values `x` and `y`, `x.equals(y)` should return `true` if and only if  `y.equals(x)` returns `true`. 
- It is ***transitive***: for any non-null reference values `x`, `y`, and `z`, if  `x.equals(y)` returns `true` and   `y.equals(z)` returns `true`, then     `x.equals(z)` should return `true`. 
- It is ***consistent***: for any non-null reference values     `x` and `y`, multiple invocations of     `x.equals(y)` consistently return `true`     or consistently return `false`, provided no information used in `equals` comparisons on the     objects is modified. 
- For any non-null reference value `x`,  `x.equals(null)` should return `false`. 

The `equals` method for class `Object` implements the most discriminating possible equivalence relation on objects; that is, for any non-null reference values `x` and `y`, this method returns `true` if and only if `x` and `y` refer to the same object (`x == y` has the value `true`). 

Note that it is generally necessary to override the `hashCode` method whenever this method is overridden, so as to maintain the general contract for the `hashCode` method, which states that equal objects must have equal hash codes.



# References

JDK 8 Source

[Object.html#hashCode()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode())

[System.html#identityHashCode](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#identityHashCode-java.lang.Object-)

[Object.html#equals()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object))



---

created at 2017-04-26 22:31