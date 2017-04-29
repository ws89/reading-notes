# Primitive Data Types

- **byte**: The `byte` data type is an 8-bit signed two's complement integer. It has a minimum value of -128 and a maximum value of 127 (inclusive). 
- **short**: The `short` data type is a 16-bit signed two's complement integer. It has a minimum value of -32,768 and a maximum value of 32,767 (inclusive). 
- **int**: By default, the `int` data type is a 32-bit signed two's complement integer, which has a minimum value of -2^31 and a maximum value of 2^31-1. In Java SE 8 and later, you can use the `int` data type to represent an unsigned 32-bit integer, which has a minimum value of 0 and a maximum value of 2^32-1.Use the Integer class to use `int` data type as an unsigned integer. 
- **long**: The `long` data type is a 64-bit two's complement integer. The signed long has a minimum value of -263 and a maximum value of 263-1. In Java SE 8 and later, you can use the `long` data type to represent an unsigned 64-bit long, which has a minimum value of 0 and a maximum value of 264-1. 
- **float**: The `float` data type is a single-precision 32-bit IEEE 754 floating point. Use a `float` (instead of `double`) if you need to save memory in large arrays of floating point numbers. This data type should never be used for precise values, such as currency. For that, you will need to use the [java.math.BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html) class instead. 
  - A constant holding the largest positive finite value of type `float`, (2-2-23)·2127.
  - A constant holding the smallest positive nonzero value of type `float`, 2-149.
  - 1.4**E**-45 <= f <= 3.4028235**E**38
- **double**:The `double` data type is a double-precision 64-bit IEEE 754 floating point. For decimal values, this data type is generally the default choice. As mentioned above, this data type should never be used for precise values, such as currency.
  - A constant holding the largest positive finite value of type `double`, (2-2-52)·21023. 
  - A constant holding the smallest positive nonzero value of type `double`, 2-1074. 
  - 4.9**E**-324 <= d <= 1.7976931348623157**E**308
- **boolean**: The `boolean` data type has only two possible values: `true` and `false`. This data type represents one bit of information, but its "size" isn't something that's precisely defined.
- **char**:The `char` data type is a single 16-bit Unicode character. It has a minimum value of `'\u0000'` (or 0) and a maximum value of `'\uffff'` (or 65,535 inclusive).



### String

- In addition to the eight primitive data types listed above, the Java programming language also provides special support for character strings
   via the [java.lang.String](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html) class.
- Enclosing your character string within double quotes will automatically create a new `String` object; for example, `String s = "this is a string";`
- **`String` objects are *immutable***, which means that once created, their values cannot be changed.
- The `String` class is not technically a primitive data type.**In the Java programming language, strings are objects.**



### Source Code

> java.lang.Double#MAX_VALUE

```java
public static final double MAX_VALUE = 0x1.fffffffffffffP+1023; // 1.7976931348623157e+308
```



> java.lang.Double#MIN_VALUE

```java
public static final double MIN_VALUE = 0x0.0000000000001P-1022; // 4.9e-324
```



> java.lang.Float#MAX_VALUE

```java
public static final float MAX_VALUE = 0x1.fffffeP+127f; // 3.4028235e+38f
```



> java.lang.Float#MIN_VALUE

```java
public static final float MIN_VALUE = 0x0.000002P-126f; // 1.4e-45f
```



### Practice

```java
@Test
public void test(){
  System.out.println( Double.MAX_VALUE );//1.7976931348623157E308
  System.out.println( Double.MAX_VALUE / 1000);//1.7976931348623156E305

  System.out.println( Double.MIN_VALUE );//4.9E-324
  System.out.println( Double.MIN_VALUE * 1000 );//4.94E-321

  double d = 22222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222.11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111122222222222222222222222222222222222222222222222;
  //2 has 308 digits before the dot
  System.out.println( d );//output:2.2222222222222221E307

  double dd = 222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222.11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111122222222222222222222222222222222222222222222222;
  //2 has 309 digits before the dot
  //compile error:Floating point number too large
  
  double d1 = 1.79E308;
  double d2 = 4.9E-324;
  System.out.println( d1 );//1.79E308
  System.out.println( d2 );//4.9E-324
  
  float f1 = Float.MAX_VALUE;
  float f2 = Float.MIN_VALUE;
  System.out.println( f1 );//3.4028235E38
  System.out.println( f2 );//1.4E-45
}
```



# Default Values

| **Data Type**          | **Default Value (for fields)** |
| ---------------------- | ------------------------------ |
| byte                   | 0                              |
| short                  | 0                              |
| int                    | 0                              |
| long                   | 0L                             |
| float                  | 0.0f                           |
| double                 | 0.0d                           |
| char                   | '\u0000'                       |
| String (or any object) | null                           |
| boolean                | false                          |



- Relying on such default values, however, is generally considered **bad programming style**.
- Local variables are slightly different; **the compiler never assigns a default value to an uninitialized local variable**. If you cannot initialize your local variable where it is declared, make sure to assign it a value before you attempt to use it. **Accessing an uninitialized local variable will result in a compile-time error**.

### Practice

```java
public class PrimitiveTest {
    int i ;

    @Test
    public void test(){
        System.out.println( i );//output:0
    }
}
```

```java
public class PrimitiveTest {
    int i ;

    @Test
    public void test2(){
        int i ;
        System.out.println( i );//Variable 'i' might not have been initialized
    }
}
```

```java
public class PrimitiveTest {
    int i ;

    @Test
    public void test2(){
        int i = 2;
        System.out.println( i );//output:2
        instanceVariablesTest();
    }
    public void instanceVariablesTest(){
        System.out.println( i );//output:0
    }
}
```



# Literals

You may have noticed that the `new` keyword isn't used when initializing a variable of a primitive type. Primitive types are special data types built into the language; they are not objects created from a class. A *literal* is the source code representation of a fixed value; literals are represented directly in your code without requiring computation. As shown below, it's possible to assign a literal to a variable of a primitive type:

```java
boolean result = true;
char capitalC = 'C';
byte b = 100;
short s = 10000;
int i = 100000;
```



### special escape literals

`\b` (backspace), `\t` (tab), `\n` (line feed), `\f` (form feed), `\r` (carriage return), `\"` (double quote), `\'` (single quote), and `\\` (backslash)



### Using Underscore Characters in Numeric Literals

In Java SE 7 and later, any number of underscore characters (`_`) can appear anywhere between digits in a numerical literal. 

The following example shows other ways you can use the underscore in numeric literals:

```java
long creditCardNumber = 1234_5678_9012_3456L;
long socialSecurityNumber = 999_99_9999L;
float pi =  3.14_15F;
long hexBytes = 0xFF_EC_DE_5E;
long hexWords = 0xCAFE_BABE;
long maxLong = 0x7fff_ffff_ffff_ffffL;
byte nybbles = 0b0010_0101;
long bytes = 0b11010010_01101001_10010100_10010010;
```



# References

[Primitive Data Types](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

[java.lang.Integer](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/java/lang/Integer.java#Integer.0MAX_VALUE)

[java.lang.Double](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/java/lang/Double.java#Double.0MAX_VALUE)

JDK 8 Source Code

[Class Float](https://docs.oracle.com/javase/8/docs/api/java/lang/Float.html#MAX_VALUE)

[Class Double](https://docs.oracle.com/javase/8/docs/api/java/lang/Double.html#MAX_VALUE)



---

created at 2017-04-29 23:27