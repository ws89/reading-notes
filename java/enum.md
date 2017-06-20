# Java Enums

A *Java Enum* is a special Java type used to define collections of constants. More precisely, a Java enum type is a special kind of Java class. An enum can contain constants, methods etc.



## Enum Example

```java
public enum Level {
    HIGH,
    MEDIUM,
    LOW
}
```



## Enum Iteration

All enum types get a static `values()` method automatically by the Java compiler. 

```java
for (Level level : Level.values()) {
    System.out.println(level);
}
```



## Enum Fields

```java
public enum Level {
    HIGH  (3),  //calls constructor with value 3
    MEDIUM(2),  //calls constructor with value 2
    LOW   (1)   //calls constructor with value 1
    ; // semicolon needed when fields / methods follow


    private final int levelCode;

    private Level(int levelCode) {
        this.levelCode = levelCode;
    }
}
```

**The `enum` constructor must be either `private` or package scope (default). You cannot use  `public` or `protected` constructors for a Java `enum`.**



## Enum Methods

```java
public enum Level {
    HIGH  (3),  //calls constructor with value 3
    MEDIUM(2),  //calls constructor with value 2
    LOW   (1)   //calls constructor with value 1
    ; // semicolon needed when fields / methods follow


    private final int levelCode;

    Level(int levelCode) {
        this.levelCode = levelCode;
    }
    
    public int getLevelCode() {
        return this.levelCode;
    }
    
}
```



## Enum Miscellaneous Details

**Java enums extend the `java.lang.Enum` class implicitly, so your enum types cannot extend another class.**

If a Java enum contains fields and methods, the definition of fields and methods must always come *after* the list of constants in the enum. Additionally, the list of enum constants must be terminated by a semicolon;



## Practice

```java
/**
 * Created by WS on 2017/6/20.
 */
public enum EnumTest {

    INSTANCE(1),
    INSTANCE2(1,2)
    ;

    private final int code;
    private int code2 ;

    private EnumTest(int code){
        this.code = code;
    }

    private EnumTest(int code,int code2){
        this.code = code;
        this.code2 = code2;
    }

    public int getCode(){
        return this.code;
    }
}
```



# References

[Java Enums](http://tutorials.jenkov.com/java/enums.html)

---

created at 2017-06-20 12:09