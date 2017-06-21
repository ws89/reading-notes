# Java Inheritance

![The class Car and Truck inherits from the class Vehicle.](http://tutorials.jenkov.com/images/java/inheritance-1.png)

### What is Inherited?

When a subclass extends a superclass in Java, all `protected` and `public` fields and methods of the superclass are inherited by the subclass. By *inherited* is meant that these fields and methods are part of of the subclass, as if the subclass had declared them itself. `protected` and `public` fields can be called and referenced just like the methods declared directly in the subclass.

Fields and methods with default (package) access modifiers can be accessed by subclasses only if the subclass is located    in the same package as the superclass. Private fields and methods of the superclass can never be referenced    directly by subclasses. They can, however, be referenced indirectly via methods reachable from the subclass    (e.g default (package), `protected` and `public` methods).

Constructors are not inherited by subclasses, but a subclass constructor must call a constructor in the superclass.    



## Overriding Methods

In Java you cannot override private methods from a superclass. If the superclass calls a private method internally from some other method, it will continue to call that method from the superclass, even if you create a private method in the subclass with the same signature.



### Practice

```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {

    @Test
    public void test(){
        Super object = new Sub();
        object.defaultMethod();
    }
}

class Super{
    private void privateMethod(){
        System.out.println( "super class private method" );
    }
    void defaultMethod(){
        System.out.println( "super class default access method " );
        privateMethod();
    }
}

class Sub extends Super{
    private void privateMethod(){
        System.out.println( "sub class private method" );
    }
}
```

output:

```
super class default access method 
super class private method
```



### Calling Superclass Methods

#### Practice

```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {

    @Test
    public void test2(){
        Super1 object = new Sub1();
        object.printText();
    }
}

class Super1{
    String text = "super text";

    void printText(){
        System.out.println( text );
    }
}
class Sub1 extends Super1{

}
```

output:

```
super text
```



```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {

    @Test
    public void test2(){
        Super1 object = new Sub1();
        object.printText();
    }
}

class Super1{
    String text = "super text";

    void printText(){
        System.out.println( text );
    }
}
class Sub1 extends Super1{
	String text = "sub text";
}
```

output:

```
super text
```



```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {

    @Test
    public void test2(){
        Super1 object = new Sub1();
        object.printText();
    }
}

class Super1{
    String text = "super text";

    void printText(){
        System.out.println( text );
    }
}
class Sub1 extends Super1{
    @Override
    void printText(){
        System.out.println( this.text );
    }
}
```

output:

```
super text
```



``` java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {

    @Test
    public void test2(){
        Super1 object = new Sub1();
        object.printText();
    }
}

class Super1{
    String text = "super text";

    void printText(){
        System.out.println( text );
    }
}
class Sub1 extends Super1{
    String text = "sub text";
    @Override
    void printText(){
        System.out.println( this.text );
      	System.out.println( super.text );
    }
}
```

output:

```
sub text
super text
```



## The instanceof Instruction

### practice

```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {
    @Test
    public void test3(){
        Super object = new Sub();
        System.out.println( object instanceof Sub );//true
        System.out.println( object instanceof Super );//true
        System.out.println( object instanceof Serializable);//false
        System.out.println( object instanceof Cloneable );//true

        Sub object2 = new Sub();
        System.out.println( object2 instanceof Super );//true
    }
}
class Super{
    private void privateMethod(){
        System.out.println( "super class private method" );
    }
    void defaultMethod(){
        System.out.println( "super class default access method " );
        privateMethod();
    }
}

class Sub extends Super implements Cloneable{
    private void privateMethod(){
        System.out.println( "sub class private method" );
    }
}
```



## Fields and Inheritance

As mentioned earlier, in Java fields cannot be overridden in a subclass. If you define a field in a subclass    with the same name as a field in the superclass, the field in the subclass will hide (shadow) the field    in the superclass. If the subclass tries to access the field, it will access the field in the subclass.

If, however, the subclass calls up into a method in the superclass, and that method accesses the field    with the same name as in the subclass, it is the field in the superclass that is accessed.

Here is Java inheritance example that illustrates how fields in subclasses shadow (hides) fields in superclasses:

```java
public class Vehicle {

    String licensePlate = null;

    public void setLicensePlate(String licensePlate) {
        this.licensePlate = licensePlate;
    }

    public String getLicensePlate() {
        return licensePlate;
    }
}
```

```java
public class Car extends Vehicle {

    protected String licensePlate = null;

    @Override
    public void setLicensePlate(String license) {
        super.setLicensePlate(license);
    }

    @Override
    public String getLicensePlate() {
        return super.getLicensePlate();
    }

    public void updateLicensePlate(String license){
        this.licensePlate = license;
    }
}
```

Notice how both classes have a `licensePlate` field defined.

Both the `Vehicle` class and `Car` class has the methods    `setLicensePlate()` and `getLicensePlate()`. The methods    in the `Car` class calls the corresponding methods in the `Vehicle`    class. The result is, that eventually both set of methods access the `licensePlate`    field in the `Vehicle` class.

The `updateLicensePlate()` method in the `Car` class however, accesses    the `licensePlate` field directly. Thus, it accesses the `licensePlate`    field of the `Car` class. Therefore, you will not get the same result if you    call `setLicensePlate()` as when you call the `updateLicense()` method.

Look at the following lines of Java code:

```
Car car = new Car();

car.setLicensePlate("123");
car.updateLicensePlate("abc");

System.out.println("license plate: "
        + car.getLicensePlate());

```

This Java code will print out the text `123`.

The `updateLicensePlate()` method sets the license plate value on the    `licensePlate` field in the `Car` class. The    `getLicensePlate()` method, however, returns the value of the `licensePlate`    field in the `Vehicle` class. Therefore, the value `123` which is set    as value for the `licensePlate` field in the `Vehicle` class via the    `setLicensePlate()` method, is what is printed out.



## Nested Classes and Inheritance

The same Java inheritance rules apply to [nested classes](http://tutorials.jenkov.com/java/nested-classes.html). Nested classes which    are declared `private` are not inherited. Nested classes with the default (package) access modifier    are only accessible to subclasses if the subclass is located in the same package as the superclass. Nested classes    with the `protected` or `public` access modifier are always inherited by subclasses.

Here is a nested class inheritance example:

```java
class MyClass {

    class MyNestedClass {

    }
}

public class MySubclass extends MyClass {

    public static void main(String[] args) {
        MySubclass subclass = new MySubclass();

        MyNestedClass nested =  subclass.new MyNestedClass();
    }
}
```

Notice how it is possible to create an instance of the nested class `MyNestedClass` which is defined    in the superclass (`MyClass`) via a reference to the subclass (`MySubclass`).



### Practice

``` java
public class ExtendTest {
    private ExtendTest(){
        System.out.println("ExtendTest private Constructor...");
    }

    static class Child extends ExtendTest{
        public Child(){
        }
    }

    @Test
    public void test(){
        Child child = new Child();
    }

    public static void main(String[]args){
        Child child = new Child();//output:ExtendTest private Constructor...      	
      	//succeed,but must static class,else compile error.
    }
}
```



## Final Classes and Inheritance

A class can be declared `final`. Here is now that looks:

```java
public final class MyClass {

}
```

A `final` class cannot be extended. In other words, you cannot inherit from a `final` class    in Java.



## Abstract Classes and Inheritance

An `abstract` class is a class that does not    contain the full implementation of whatever the `abstract` class should do. Thus, it cannot be    instantiated. In other words, you cannot create objects of an `abstract` class.

In Java `abstract` classes are intended to be extended to create a full implementation. Thus, it is    fully possible to extend an `abstract` class. The Java inheritance rules are the same for `abstract`    classes as for non-abstract classes.

### Practice

```java
/**
 * Created by WS on 2017/6/21.
 */
public class Inheritance {
    @Test
    public void test4(){
        AbstractClass abstractClass = new AbstractClass() {
        };
    }
}

abstract class AbstractClass{
  
}
```



# References

[Java Inheritance](http://tutorials.jenkov.com/java/inheritance.html)



---

created at 2017-06-21 21:31