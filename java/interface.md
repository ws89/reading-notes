# Java Interfaces



## Java Interface Example

```java
public interface MyInterface {

    public String hello = "Hello";

    public void sayHello();
}
```

As you can see, an interface is declared using the Java `interface` keyword. Just like with classes, **a Java interface can be declared `public` or package scope (no access modifier).**



The interface example above contains one variable and one method. The variable can be accessed directly from the interface, like this:

```java
System.out.println(MyInterface.hello);
```

As you can see, **accessing a variable from an interface is very similar to accessing a static variable in a class**.



## Implementing an Interface

```java
public class MyInterfaceImpl implements MyInterface {

    public void sayHello() {
        System.out.println(MyInterface.hello);
    }
}
```

A class that implements an interface **must implement all the methods declared in the interface**. The methods must have the exact same signature (name + parameters) as declared in the interface. The class does not need to implement (declare) the variables of an interface. **Only the methods**.



### Interface Instances

Once a Java class implements an Java interface you can use an instance of that class as an instance of that interface. Here is an example:

```java
MyInterface myInterface = new MyInterfaceImpl();

myInterface.sayHello();
```

Notice how the variable is declared to be of the interface type `MyInterface`  while the object created is of type `MyInterfaceImpl`. Java allows this because the class    `MyInterfaceImpl` implements the `MyInterface` interface. You can then reference instances of the `MyInterfaceImpl` class as instances of the `MyInterface`  interface.



### Implementing Multiple Interfaces

A Java class can implement multiple Java interfaces. In that case the class must implement all the methods declared in all the interfaces implemented. Here is an example:


```java
public class MyInterfaceImpl implements MyInterface, MyOtherInterface {
  public void sayHello() {
      System.out.println("Hello");
  }

  public void sayGoodbye() {
      System.out.println("Goodbye");
  }
}
```
This class implements two interfaces called MyInterface and MyOtherInterface. You list the names of the interfaces to implement after the implements keyword, separated by a comma.

If the interfaces are not located in the same packages as the implementing class, you will also need to import the interfaces. Java interfaces are imported using the import instruction just like Java classes. For instance:

```java
import com.jenkov.package1.MyInterface;
import com.jenkov.package2.MyOtherInterface;
public class MyInterfaceImpl implements MyInterface, MyOtherInterface {
    ...
}
```
Here are the two Java interfaces implemented by the class above:

```java
public interface MyInterface {
	public void sayHello();
}
```
```java
public interface MyOtherInterface {
	public void sayGoodbye();
}
```
As you can see, each interface contains one method. These methods are implemented by the class MyInterfaceImpl. 



#### Practice

```java
@Test
public void test(){
    System.out.println( MyInterface.s );//s

    //MyInterface.s = "ss"; //compile error:Cannot assign a value to final variable 's'

    MyInterfaceImpl myInterfaceImpl = new MyInterfaceImpl();
    myInterfaceImpl.sayHello();
}
```

```java
interface MyInterface{
    String s = "s";
    String ss = "ss";

    void sayHello();
}
interface MyInterface2{
    String ss = "ss";
    String s1 = "s1";

}

class MyInterfaceImpl implements MyInterface,MyInterface2{
    String s1 = "s1-subclass";
    @Override
    public void sayHello() {
        System.out.println( s );//s
    //    System.out.println( ss );//compile error:Reference to 'ss' is ambiguous, both 'MyInterface.ss' and 'MyInterface2.ss' match
        System.out.println( s1 );//s1-subclass
    }
}
```



### Overlapping Method Signatures

If a Java class implements multiple Java interfaces, there is a risk that some of these interfaces may contain methods with the same signature (name + parameters). Since a Java class can only  implement at method with a given signature once, this could potentially lead to some problems.

The Java specification does not give any solution to this problem. It is up to you to decide what to do in that situation.

#### Practice

```java
interface SameMethodInterface{
    void method();
}
interface SameMethodInterface2{
    int method();
}
class SameMethodInterfaceImpl implements SameMethodInterface,SameMethodInterface2{

    @Override
    public void method() {
        /*
            compile error:
            'method()' in 'io.github.ws89.notes.java.SameMethodInterfaceImpl' clashes with 'method()' in 'io.github.ws89.notes.java.SameMethodInterface2'; attempting to use incompatible return type
         */
    }
}
```



## Interface Variables

A Java interface can contain both variables and constants. However, often it does not makes sense to    place variables in an interface. In some cases it can make sense to define constants in an    interface. Especially if those constants are to be used by the classes implementing the    interface, e.g. in calculations, or as parameters to some of the methods in the interface.    However, my advice to you is to avoid placing variables in Java interfaces if you can.

All variables in an interface are public, even if you leave out the `public` keyword in the variable declaration.



## Interface Methods

A Java interface can contain one or more method declarations. As mentioned earlier, the interface cannot specify any implementation for these methods. It is up to the classes implementing the interface    to specify an implementation.

All methods in an interface are public, even if you leave out the `public` keyword in the method declaration.



## Interface Default Methods

Before Java 8 Java interfaces could not contain an implementation of the methods, but only contain the method signatures. However, this results in some problems when an API needs to add a method to one of its interfaces. If the API just adds the method to the desired interface, all classes that implements the interface must implement    that new method. That is fine if all implementing classes are located within the API. But if some implementing classes are located in client code of the API (the code that uses the API), then that code breaks.

Let me illustrate this with an example. Look at this interface and imagine that it is part of e.g. an open source API which many applications are using internally:

```java
public interface ResourceLoader {

    Resource load(String resourcePath);

}
```

Now imagine that a project uses this API and has implemented the `ResourceLoader` interface like this:

```java
public class FileLoader implements ResourceLoader {

    public Resource load(String resourcePath) {
        // in here is the implementation +
        // a return statement.
    }
}
```

If the developer of the API wants to add one more method to the `ResourceLoader` interface, then the `FileLoader` class will be broken when that project upgrades to the new version of the API.

To alleviate this Java interface evolution problem, Java 8 has added the concept of *interface default methods*  to Java interfaces. An interface default method can contain a default implementation of that method. Classes that implement the interface but which contain no implementation for the default interface will then automatically get the default method implementation.

You mark a method in an interface as a default method using the `default` keyword. Here is an example    of adding a default method to the `ResourceLoader` interface:

```java
public interface ResourceLoader {

    Resource load(String resourcePath);

    default Resource load(Path resourcePath) {
        // provide default implementation to load
        // resource from a Path and return the content
        // in a Resource object.
    }

}
```

This example adds the default method `load(Path)`. The example leaves out the actual implementation    (inside the method body) because this is not really interesting. What matters is how you declare the    interface default method.

A class can override the implementation of a default method simply by implementing that method explicitly, as is done normally when implementing a Java interface. **Any implementation in a class takes precedence over interface default method implementations**.



#### Practice

```java
@Test
public void defaultMethod(){
    DefaultMethodInterface defaultMethodInterface = new DefaultMethodInterfaceImpl();
    defaultMethodInterface.defaultMethod();//implement method in implement class
    defaultMethodInterface.defaultMethod2();//default method 2 in Interface
}
```

```java
interface DefaultMethodInterface{
    default void defaultMethod(){
        System.out.println( "default method in Interface" );
    }

    default void defaultMethod2(){
        System.out.println( "default method 2 in Interface" );
    }
}

class DefaultMethodInterfaceImpl implements DefaultMethodInterface{
    @Override
    public void defaultMethod() {
        System.out.println("implement method in implement class ");
    }
}
```



## Interfaces and Inheritance

It is possible for a Java interface to inherit from another Java interface, just like classes can inherit from other classes. You specify inheritance using the `extends` keyword. Here is a simple interface inheritance example:

```java
public interface MySuperInterface {

    public void saiHello();

}
```

```java
public interface MySubInterface extends MySuperInterface {

    public void sayGoodbye();
}
```

The interface `MySubInterface` extends the interface `MySuperInterface`.    That means, that the `MySubInterface` inherits all field and methods from `MySuperInterface`.    That then means, that if a class implements `MySubInterface`, that class has to implement    all methods defined in both `MySubInterface` and `MySuperInterface`.

**It is possible to define methods in a subinterface with the same signature (name + parameters) as methods defined in a superinterface, should you find that desirable in your design, somehow.**

Unlike classes, interfaces can actually inherit from multiple superinterfaces. You specify that by listing the names of all interfaces to inherit from, separated by comma. A class implementing    an interface which inherits from multiple interfaces must implement all methods from the interface    and its superinterfaces.

Here is an example of a Java interface that inherits from multiple interfaces:

```java
public interface MySubInterface extends
    SuperInterface1, SuperInterface2 {

    public void sayItAll();
}
```

**As when implementing multiple interfaces, there are no rules for how you handle the situation when multiple superinterfaces have methods with the same signature (name + parameters).**



#### Practice

```java
interface InterfaceA{
    void method();
}
interface InterfaceB extends InterfaceA{
    int method();
    // 'method()' in 'io.github.ws89.notes.java.InterfaceB' clashes with 'method()' in 'io.github.ws89.notes.java.InterfaceA'; attempting to use incompatible return type
}
```

```java
interface InterfaceC{
    void method();
}
interface InterfaceD{
    int method();
}
interface InterfaceE extends InterfaceC,InterfaceD{
//'method()' in 'io.github.ws89.notes.java.InterfaceD' clashes with 'method()' in 'io.github.ws89.notes.java.InterfaceC'; methods have unrelated return types
}
```



### Inheritance and Default Methods

Interface default methods add a bit complexity to the rules of interface inheritance. While it is normally possible    for a class to implement multiple interfaces even if the interfaces contain methods with the same signature,    this is not possible if one or more of these methods are default methods. In other words, if two interfaces    contain the same method signature (name + parameters) and one of the interfaces declare this method as a default    method, a class cannot automatically implement both interfaces.

The situation is the same if an interface extends (inherits from) multiple interfaces, and one or more of these interfaces contain methods with the same signature, and one of the superinterfaces declare the overlapping method    as a default method.

In both of the above situations the **Java compiler requires that the class implementing the interface(s) explicitly implements the method which causes the problem.** That way there is no doubt about which implementation the class will have. The implementation in the class takes precedence over any default implementations.



#### Practice

```java
interface InterfaceF{
    void method();
}
interface InterfaceG{
    default void method(){
        System.out.println("default implementation");
    }
}

class InterfaceFGImpl implements InterfaceF,InterfaceG{
//compile error:
// Class 'InterfaceFGImpl' must either be declared abstract or implement abstract method 'method()' in 'InterfaceF'.
// io.github.ws89.notes.java.InterfaceFGImpl inherits unrelated defaults for method() from types io.github.ws89.notes.java.InterfaceF and io.github.ws89.notes.java.InterfaceG.
}
```



## Interfaces and Polymorphism

Java interfaces are a way to achieve polymorphism. Polymorphism is a concept that takes some practice and thought to master. Basically, **polymorphism means that an instance of an class (an object) can be used as if it were of different types.** Here, a type means either a class or an interface.

| ![Two parallel class hierarchies used in the same application.](http://tutorials.jenkov.com/images/java/interfaces-1.png) |
| ---------------------------------------- |
|                                          |

The classes above are all parts of a model representing    different types of vehicles and drivers, with fields and methods. That is the responsibility of these classes - to model    these entities from real life.

Now imagine you needed to be able to store these objects in a database, and also serialize them to    XML, JSON, or other formats. You want that implemented using a single method for each operation, available    on each `Car`, `Truck` or `Vehicle` object.    A `store()` method, a `serializeToXML()` method and a `serializeToJSON()` method.

Please forget for a while, that implementing this functionality as methods directly on the objects    may lead to a messy class hierarchy. Just imagine that this is how you want the operations implemented.

Where in the above diagram would you put these three methods, so they are accessible on all classes?

One way to solve this problem would be to create a common superclass for the `Vehicle`    and `Driver` class, which has the storage and serialization methods. However, this would result in a conceptual mess. The class hierarchy would no longer model vehicles and drivers, but also be tied to the storage and serialization mechanisms used in your application.

A better solution would be to create some interfaces with the storage and serialization methods on,    and let the classes implement these interfaces. Here are examples of such interfaces:

```java
public interface Storable {

    public void store();
}
```

```java
public interface Serializable {
    public void serializeToXML(Writer writer);
    public void serializeToJSON(Writer writer);
}
```

When each class implements these two interfaces and their methods, you can access    the methods of these interfaces by casting the objects to instances of the interface types. You don't need    to know exactly what class a given object is of, as long as you know what interface it implements.    Here is an example:

```java
Car car = new Car();

Storable storable = (Storable) car;
storable.store();

Serializable serializable = (Serializable) car;
serializable.serializeToXML (new FileWriter("car.xml"));
serializable.serializeToJSON(new FileWriter("car.json"));
```

As you can probably imagine by now, interfaces provide a cleaner way of implementing cross cutting functionality in classes than inheritance.



# References

[Java Interfaces](http://tutorials.jenkov.com/java/interfaces.html)

---

created at 2017-06-16 22:19