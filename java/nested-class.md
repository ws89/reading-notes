# Java Nested Classes

In Java nested classes are classes that are defined inside another class.

The purpose of a nested class is to clearly group the nested class with its surrounding class, signaling that these two classes are to be used together. Or perhaps that the nested class is only to be used from inside its enclosing (owning) class.

In Java nested classes are considered members of their enclosing class. Thus, a nested class **can be declared `public`, `package` (no access modifier), `protected` and `private` .**Therefore nested classes in Java can also be inherited by subclasses.

The different Java nested class types are:

- Static nested classes
- Non-static nested classes
- Local classes
- Anonymous classes



## Static Nested Classes

Static nested classes are declared in Java like this:

```java
public class Outer {

  public static class Nested {
  }

}
```

In order to create an instance of the `Nested` class you must reference it by prefixing it with the `Outer` class name, like this:

```java
Outer.Nested instance = new Outer.Nested();
```



## Non-static Nested Classes (Inner Classes)

Non-static nested classes in Java are also called *inner classes*. Inner classes are associated with an instance of the enclosing class. Thus, you must first create an instance of the enclosing class to create an instance of an inner class. Here is an example inner class definition:

```java
public class Outer {

  public class Inner {
  }

}
```

Here is how you create an instance of the `Inner` class:

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

Notice how you put `new` after the reference to the outer class in order to create an instance of the inner class.

Non-static nested classes (inner classes) have access to the fields of the enclosing class, even if they are declared private. Here is an example of that:

```java
public class Outer {

    private String text = "I am private!";

    public class Inner {

        public void printText() {
            System.out.println(text);
        }
    }
}
```

Notice how the `printText()` method of the `Inner` class references the private `text` field of the `Outer` class. This is perfectly possible. Here is how you would call the `printText()` method:

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.printText();
```



### Inner Class Shadowing

If a Java inner class declares fields or methods with the same names as field or methods in its enclosing class, the inner fields or methods are said to *shadow* over the outer fields or methods. Here is an example:

```java
public class Outer {

    private String text = "I am Outer private!";

    public class Inner {

        private String text = "I am Inner private";

        public void printText() {
            System.out.println(text);
        }
    }
}
```

In the above example both the `Outer` and `Inner` class contains a field named `text`. When the `Inner` class refers to `text` it refers to its own field. When `Outer` refers to `text` it also refers to its own field.

Java makes it possible though, for the `Inner` class to refer to the `text` field of the `Outer` class. To do so it has to prefix the `text` field reference with `Outer.this.` (the outer class name + `.this.` + field name) like this:

```java
public class Outer {

    private String text = "I am Outer private!";

    public class Inner {

        private String text = "I am Inner private";

        public void printText() {
            System.out.println(text);
            System.out.println(Outer.this.text);
        }
    }
}
```

Now the `Inner.printText()`  method will print both the `Inner.text` and `Outer.text` fields.



## Local Classes

Local classes in Java are like inner classes (non-static nested classes) that are defined inside a method or scope block (`{ ... }`) inside a method. Here is an example:

```java
class Outer {

    public void printText() {

        class Local {
        }

        Local local = new Local();
    }

}
```

Local classes can only be accessed from inside the method or scope block in which they are defined.

Local classes can access members (fields and methods) of its enclosing class just like regular inner classes.

Local classes can also access local variables inside the same method or scope block, provided these variables are declared `final`.

From Java 8 local classes can also access local variables and parameters of the method the local class is declared in. The parameter will have to be declared `final` or be *effectually final*. Effectually final means that the variable is never changed after it is initialized. Method parameters are often effectually final.

Local classes can also be declared inside static methods. In that case the local class only has access to the static parts of the enclosing class. Local classes cannot contain all kinds of static declarations (constants are allowed - variables declared `static final`), because local classes are non-static in nature - even if declared inside a static method.

The same shadowing rules apply for local classes as for inner classes.



## Anonymous Classes

Anonymous classes in Java are nested classes without a class name. They are typically declared as either subclasses of    an existing class, or as implementations of some [interface](http://tutorials.jenkov.com/java/interfaces.html).

Anonymous classes are defined when they are instantiated. Here is an example that declares an anonymous    subclass of a superclass called `SuperClass`:

```java
public class SuperClass {

  public void doIt() {
    System.out.println("SuperClass doIt()");
  }

}

SuperClass instance = new SuperClass() {

    public void doIt() {
        System.out.println("Anonymous class doIt()");
    }
};

instance.doIt();
```

Running this Java code would result in `Anonymous class doIt()` being printed to `System.out`. The anonymous class subclasses (extends) `SuperClass` and overrides the `doIt()` method.



A Java anonymous class can also implement an interface instead of extending a class.    Here an example:

```java
public interface MyInterface {

  public void doIt();

}
```

```java
MyInterface instance = new MyInterface() {

    public void doIt() {
        System.out.println("Anonymous class doIt()");
    }
};

instance.doIt();
```

As you can see, an anonymous class implementing an interface is pretty similar to an anonymous class extending another class.

An anonymous class can access members of the enclosing class. It can also access local variables which are declared final or effectively final (since Java 8).



You can declare fields and methods inside an anonymous class, but **you cannot declare a constructor**. You can declare a static initializer for the anonymous class instead, though. Here is an example:

```java
final Strint textToPrint = "Text...";

MyInterface instance = new MyInterface() {

    private String text;

    //static initializer
    {  this.text = textToPrint;  }

    public void doIt() {
        System.out.println(this.text);
    }
};

instance.doIt();
```

The same shadowing rules apply to anonymous classes as to inner classes.



## Nested Class Benefits

The benefits of Java nested classes are that you can group classes together that belong together. You could do so already by putting them in the same package, but putting one class inside another makes an even stronger grouping.

A nested class is typically only used *by* or *with* its enclosing class. Sometimes a nested class is only visible to the enclosing class, is only used internally, and is thus never visible outside the enclosing class. Other times the nested class is visible outside its enclosing class, but can only be used in conjunction with the enclosing class.



An example would be a `Cache` class. Inside the `Cache` class you might    declare a `CacheEntry` class which can contain information about a specific cache    entry (cached value, time inserted, number of times accessed etc.). Users of the `Cache` class    may never see the `CacheEntry` class, if they have no need to obtain information    about the `CacheEntry` itself, but only the cached value. However, the `Cache`    class may choose to make the `CacheEntry` class visible to the outside world, so they can    access more than just the cached value (for instance information about when the value was last refreshed etc.).

Here are two `Cache` implementation skeletons illustrating the points:

```java
public class Cache {

    private Map<String, CacheEntry> cacheMap = new HashMap<String, CacheEntry>();

    private class CacheEntry {
        public long   timeInserted = 0;
        public object value        = null;
    }

    public void store(String key, Object value){
        CacheEntry entry = new CacheEntry();
        entry.value = value;
        entry.timeInserted = System.currentTimeMillis();
        this.cacheMap.put(key, entry);
    }

    public Object get(String key) {
        CacheEntry entry = this.cacheMap.get(key);
        if(entry == null) return null;
        return entry.value;
    }

}


```



```java
public class Cache {

    private Map<String, CacheEntry> cacheMap = new HashMap<String, CacheEntry>();

    public class CacheEntry {
        public long   timeInserted = 0;
        public object value        = null;
    }

    public void store(String key, Object value){
        CacheEntry entry = new CacheEntry();
        entry.value = value;
        entry.timeInserted = System.currentTimeMillis();
        this.cacheMap.put(key, entry);
    }

    public Object get(String key) {
        CacheEntry entry = this.cacheMap.get(key);
        if(entry == null) return null;
        return entry.value;
    }

    public CacheEntry getCacheEntry(String key) {
        return this.cacheMap.get(key);
        }

}
```

The first `Cache` class hides its `CacheEntry` nested class while the second `Cache` class exposes it.



# Practice

```java
/**
 * Created by WS on 2017/6/20.
 */
public class Outer {

    private String outerPrivateValue = "outer private field";
    private String text = "outer text";


    public class PublicNestedClass{
        String s = "inner class";
        private String text = "nested text";

        void printText(){
            System.out.println( this.text );//nested text
            System.out.println( Outer.this.text );//outer text
        }
    }
    class PackagePrivateNestedClass{}
    protected class ProtectedNestedClass{}
    private class PrivateNestedClass{}


    public static class PublicStaticNested{
        static String s = "PublicStaticNested";
    }


    @Test
    public void test(){
        System.out.println( Outer.PublicStaticNested.s );//PublicStaticNested

        Outer outer = new Outer();
        Outer.PublicNestedClass inner = outer.new PublicNestedClass();
        System.out.println( inner.s + " - " + outerPrivateValue);//inner class - outer private field
        inner.printText();

        System.out.println();
    }



    void method(String methodParam){
        String methodVariable = "method variable";
        //methodParam = "be changed,should be declare final"; //if this statement occur,will compile error,because it is used to printText() method;

        /*
            methodVariable & methodParam must be final if that is used for LocalClass.
        */
        class LocalClass{
            void printText(){
                System.out.println( methodVariable );//method variable
                System.out.println( methodParam );//method param
                System.out.println( outerPrivateValue );//outer private field
            }
        }

        //use
        LocalClass localClass = new LocalClass();
        localClass.printText();
    }

    @Test
    public void testLocalClass(){
        method("method param");
    }
}
```



# References

[Java Nested Classes](http://tutorials.jenkov.com/java/nested-classes.html)



---

created at 2017-06-20 15:34