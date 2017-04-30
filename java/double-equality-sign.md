# ==

- The == is a relational operator in Java that is used to compare two operands. It is used to determine whether the two operands are equal or not. 
- Using the == operator, you can compare any primitive type such as int, char, float and Booleans. After comparison, the == operator returns a boolean value. 
- When used with objects, the == operator compares the two object references and determines whether they refer to the same instance.
- At run time, the result of `==` is `true` if the operand values are both `null` or both refer to the same object or array; otherwise, the result is `false`.
  ​               



# Practice

```java
public class DoubleEqualitySign {

    @Test
    public void test(){
        Integer i1 = 1000;
        Integer i2 = 1000;

        System.out.println( i1 == i2 );//false
        System.out.println( i1.equals(i2) );//true
        System.out.println( i1.hashCode() + "\t" + i2.hashCode());//1000   1000

        i1 = 127; i2 = 127;
        System.out.println( i1 == i2 );//true

        i1 = 128; i2 = 128;
        System.out.println( i1 == i2 );//false

        i1 = -128; i2 = -128;
        System.out.println( i1 == i2 );//true

        i1 = -129; i2 = -129;
        System.out.println( i1 == i2 );//false

        Long l1=127L,l2=127L;
        System.out.println( l1 == l2 );//true

        l1=128L; l2=128L;
        System.out.println( l1 == l2 );//false
      
      	int i3=1000,i4=1000;
        System.out.println( i3 == i4 );//true

        i1=1000;//i1 is an Integer
        System.out.println( i1 == i3 );//true
      
        Integer i5 = new Integer(10);
        Integer i6 = new Integer(10);
        System.out.println( i5 == i6 );//false      
      
        Integer i7 = Integer.valueOf(10);
        Integer i8 = Integer.valueOf(10);
        System.out.println( i7 == i8 );//true
      
      	i7 = Integer.valueOf(128);
        i8 = Integer.valueOf(128);
        System.out.println( i7 == i8 );//false

        Integer i9 = Integer.parseInt("128");
        Integer i10 = Integer.parseInt("128");
        System.out.println( i9 == i10 ); //false

        i9 = Integer.parseInt("127");
        i10 = Integer.parseInt("127");
        System.out.println( i9 == i10);//true


        i9 = Integer.parseInt("128");
        i10 = Integer.valueOf("128");
        System.out.println( i9 == i10);//false

        int i11 = 128;
        System.out.println( i9 == i11 );//true
        System.out.println( i9 == i10 );//false

        Integer i12 = 128;
        System.out.println( i12 == i9 );//false

        System.out.println( Integer.parseInt("128") == Integer.parseInt("128") );//true
        System.out.println( Integer.parseInt("128") == Integer.valueOf("128") );//true
        System.out.println( Integer.valueOf("128") == Integer.valueOf("128") );//false
      
      	int i13 = 128;
        Integer i14 = new Integer(128);
        System.out.println( i13 == i14 );//true

        i14 = Integer.valueOf("128");
        System.out.println( i13 == i14 );//true

        i14 = Integer.parseInt("128");
        System.out.println( i13 == i14 );//true

        i13 = 127;
        i14 = new Integer(127);
        System.out.println( i13 == i14 );//true
    }
}
```



```java
@Test
public void test2(){
    Double d1 = new Double(12.0);
    Double d2 = d1;
    System.out.println( d1 == d2 );//true
    d1 = d1 + 1;
    System.out.println( d1 == d2 );//false //!!!

    Integer i1 = new Integer(1000);
    Integer i2 = i1;
    System.out.println( i1 == i2 );//true

    i1 = i1 + 1;
    System.out.println( i1 == i2 );//false //!!!
  	//!!!
    System.out.println( i1 + "\t" + i2);//1001 1000
  
    Integer i3 = 2000;
    Integer i4 = i3;
    System.out.println( i3 == i4 );//true
    i3 = i3 +1;
    System.out.println( i3 + "\t" + i4 );//2001	2000 (because Integer is immutabe,final class)
}
```



> java.lang.Integer

```java
public final class Integer extends Number implements Comparable<Integer> {
	...
}
```



# Integer

- `valueOf` is returning an `Integer` object, which may have its values cached between -128 and 127. 
- **It is important to note** that you are comparing references with `Integer#valueOf`, and if you are comparing a value that is **larger than what the cache supports**, it will **not** evaluate to `true`, even if the parsed values are equivalent (case in point:  `Integer.valueOf(128) == Integer.valueOf(128)`). 
- `parseInt` is returning a primitive `int`.
- The `Integer` class has a static cache, that stores 256 special `Integer` objects - one for every value between -128 and 127.  



### Example

```java
new Integer(123);
```

> This (obviously) makes a brand new `Integer` object.



```java
Integer.parseInt("123");
```

> This returns an `int` primitive value after parsing the `String`.



```java
Integer.valueOf("123");
```

> This is more complex than the others.  It starts off by parsing the `String`.  Then, if the value is between -128 and 127, it returns the corresponding object from the static cache.  If the value is outside of this range, then it invokes `new Integer()` and passes in the value, so that you get a new object.



```java
Integer.valueOf("127")==Integer.valueOf("127");
```

> This returns true, because the `Integer` whose value is 127 is retrieved twice from the static cache, and compared to itself.  There's only one `Integer` object involved, so this returns `true`.



```java
Integer.valueOf("128")==Integer.valueOf("128");
```

> This returns `false`, because 128 is not in the static cache.  So a new `Integer` is created for each side of the equality.  Since there are two different `Integer` objects, and `==` for objects only returns `true` if both sides are the exact same object, this is going to be `false`.



```java
Integer.parseInt("128")==Integer.valueOf("128");
```

> This is comparing the primitive `int` value 128 on the left, with a newly created `Integer` object on the right.  But because it doesn't make sense to compare an `int` to an `Integer`, Java will **auto-unbox the `Integer` before doing the comparison**; so you end up comparing an `int` to an `int`.  Since the primitive 128 is equal to itself, this returns `true`.



### SourceCode

> java.lang.Integer#parseInt(java.lang.String)

```java
public static int parseInt(String s) throws NumberFormatException {
    return parseInt(s,10);
}
```

```java
public static int parseInt(String s, int radix)
            throws NumberFormatException
{ ... }
```



> java.lang.Integer#valueOf(java.lang.String)

```java
public static Integer valueOf(String s) throws NumberFormatException {
    return Integer.valueOf(parseInt(s, 10));
}
```

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```



> java.lang.Integer.IntegerCache

`IntegerCache`  is similar with `ByteCache` `ShortCache` `LongCache`

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```





# References

[What is the difference between == vs equals() in Java?](http://stackoverflow.com/questions/7520432/what-is-the-difference-between-vs-equals-in-java)

[== Versus .equals() in Java](http://www.onlinetutorialspoint.com/java/versus-equals-in-java.html)

[Why do == comparisons with Integer.valueOf(String) give different results for 127 and 128?](http://stackoverflow.com/questions/20877086/why-do-comparisons-with-integer-valueofstring-give-different-results-for-12)

JDK 8 Source

[Difference between parseInt and valueOf in java?](http://stackoverflow.com/questions/508665/difference-between-parseint-and-valueof-in-java)

[15.21.3. Reference Equality Operators `==` and `!=`](http://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.21.3)

---

created at 2017-04-27 00:11