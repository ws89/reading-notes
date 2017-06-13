# Java Generics

The Java Generics features were added to the Java language from Java 5. Generics add a way to specify concrete types to general purpose classes and methods that operated on `Object` before. 



## Java Generics Example

The `List` interface represents a list of `Object` instances.    This means that we could put any object into a `List`. Here is an example:

```java
List list = new ArrayList();

list.add(new Integer(2));
list.add("a String");
```

Because any object could be added, you would also have to cast any objects obtained    from these objects. For instance:

```java
Integer integer = (Integer) list.get(0);

String string   = (String) list.get(1);
```

Very often you only use a single type with a collection. For instance, you only    keep `String`'s or something else in the collection, and not mixed    types like I did in the example above.

With the Java Generics features you can set the type of the collection to limit    what kind of objects can be inserted into the collection. Additionally, you    don't have to cast the values you obtain from the collection. Here is    an example using Java's Generic's features:

```java
List<String> strings = new ArrayList<String>();

strings.add("a String");

String aString = strings.get(0);
```



## Java 7 Type Inference

The Java generics features were updated in Java 7. From Java 7 the Java compiler can infer the type of the  collection instantiated from the variable the collection is assigned to. Here is a Java 7 generics example:

```
List<String> strings = new ArrayList<>();
```





# Java Generic Classes

## Generic Classes in Java

It is possible to generify your own Java classes. Generics is not restricted to the predefined classes in the Java API's. Here is a simple example:

```java
public class GenericFactory<T> {

    Class theClass = null;

    public GenericFactory(Class theClass) {
        this.theClass = theClass;
    }

    public T createInstance()
    throws IllegalAccessException, InstantiationException {
        return (T) this.theClass.newInstance();
    }
}
```

The `<T>` is a type token that signals that this class can have a type set when instantiated. Here is an example of how:

```java
GenericFactory<MyClass> factory = new GenericFactory<MyClass>(MyClass.class);

MyClass myClassInstance = factory.createInstance();
```

Notice how it is not necessary to cast the object returned from the `factory.createInstance()`    method. The compiler can deduct the type of the object from the generic type of the    `GenericFactory` created, because you specified the type inside the <>.

Each instance of the `GenericFactory` can be generified to different types.    Here are two examples:

```java
GenericFactory<MyClass> factory =  new GenericFactory<MyClass>(MyClass.class);

MyClass myClassInstance = factory.createInstance();

GenericFactory<SomeObject> factory = new GenericFactory<SomeObject>(SomeObject.class);

SomeObject someObjectInstance = factory.createInstance();
```



# Java Generic Methods

It is possible to generify methods in Java. Here is an example:

```java
public static <T> T addAndReturn(T element, Collection<T> collection){
    collection.add(element);
    return element;
}
```

This method specifies a type T which is used both as type for the `element` parameter and the generic type of the `Collection`. Notice how it is now possible to **add** elements to the collection. 

```java
String stringElement = "stringElement";
List<String> stringList = new ArrayList<String>();

String theElement = addAndReturn(stringElement, stringList);    

Integer integerElement = new Integer(123);
List<Integer> integerList = new ArrayList<Integer>();

Integer theElement = addAndReturn(integerElement, integerList);    
```

Notice how we can call the `addAndReturn()` method using both `String`'s    and `Integer`'s and their corresponding collections. The compiler knows from the type  of the `T` parameter and `collection<T>` parameter definitions, that the type is to be taken from these parameters at call time (use time).

The compiler can even perform more advanced type inference. For instance, the following call is also legal:

```java
String stringElement = "stringElement";
List<Object> objectList = new ArrayList<Object>();

Object theElement = addAndReturn(stringElement, objectList);    
```

In this case we are using two different types for T: `String` and `Object`. The compiler then uses the most specific type argument that makes the method call type correct. In this case it infers **T** to be `Object`.

The inverse is not legal though:

```java
Object objectElement = new Object();
List<String> stringList = new ArrayList<String>();

Object theElement = addAndReturn(objectElement, stringList);
```

In this case the compiler infers, that for the    method call to be type safe, T must be a `String`. The `objectElement` passed in for the `T element` parameter must then also be a `String` (and it isn't). Therefore the compiler will report an error.



# Java Generics - Class Objects as Type Literals

Class objects can be used as type specifications too, at runtime. For instance, you can create a generified method like this:

```java
public static <T> T getInstance(Class<T> theClass)
    throws IllegalAccessException, InstantiationException {

    return theClass.newInstance();
}
```

Here are a few examples of calls to the `getInstance()` method:

```java
String string   = getInstance(String.class);

MyClass myClass = getInstance(MyClass.class);
```

As you can see the return type changes depending on what class object you pass in as parameter to the method. This can be quite handy in database API's like Butterfly Persistence where you read objects from a database. Here is an example method definition:

```java
public static <T> T read(Class<T> theClass, String sql)
    throws IllegalAccessException, InstantiationException {

    //execute SQL.

    T o = theClass.newInstance();
    //set properties via reflection.

    return o;
}
```

Here is how you would call the `read()` method:

```java
Driver employee = read(Driver.class, "select * from drivers where id=1");

Vehicle vehicle = read(Vehicle.class, "select * from vehicles where id=1");
```



# Java Generic's Wildcards

Java Generic's wildcards is a mechanism in Java Generics aimed at making it possible to cast a collection of a certain class, e.g A, to a collection of a subclass or superclass of A.

## The Basic Generic Collection Assignment Problem

Imagine you have the following class hierarchy:

```java
public class A { }
public class B extends A { }
public class C extends A { }
```

The classes B and C both inherit from A.

Then look at these two `List` variables:

```java
List<A> listA = new ArrayList<A>();
List<B> listB = new ArrayList<B>();
```

## Generic Wildcards

The generic wildcards target two primary needs:

- Reading from a generic collection
- Inserting into a generic collection

There are three ways to define a collection (variable) using generic wildcards. These are:

```java
List<?>           listUknown = new ArrayList<A>();
List<? extends A> listUknown = new ArrayList<A>();
List<? super   A> listUknown = new ArrayList<A>();
```



## The Unknown Wildcard

 `List<?>` means a list typed to an unknown type. This could be a `List<A>`,    a `List<B>`, a `List<String>` etc.

​    Since the you do not know what type    the `List` is typed to, you can only read from the collection, and you can only treat the objects    read as being `Object` instances. Here is an example:

```java
public void processElements(List<?> elements){
   for(Object o : elements){
      System.out.println(o);
   }
}
```

​    The `processElements()` method can now be called with any generic `List` as parameter.    For instance a `List<A>`, a `List<B>`, `List<C>`,    a `List<String>` etc. Here is a valid example:

```java
List<A> listA = new ArrayList<A>();

processElements(listA);
```

### practice

```java
@Test
public void test(){
    List<String> list = new ArrayList<>();
    list.add("a");
    list.add("b");

    processElements( list );
    processElements1( list);
}

<E> void processElements(List<E> elements){
    for(E e : elements){
        System.out.println( e );
    }
}

void processElements1(List<?> elements){
    for(Object e : elements){
        System.out.println( e );
    }
}
```



## The extends Wildcard Boundary

`List<? extends A>` means a `List` of objects that are instances of the class  A, or subclasses of A (e.g. B and C).

When you know that the instances in the collection are of instances of A or subclasses of A, it is safe to read the instances of the collection and cast them to A instances. Here is an example:

```java
public void processElements(List<? extends A> elements){
   for(A a : elements){
      System.out.println(a.getValue());
   }
}
```

You can now call the `processElements()` method with either a `List<A>`,  `List<B>` or  `List<C>`. Hence, all of these examples are valid:

```java
List<A> listA = new ArrayList<A>();
processElements(listA);

List<B> listB = new ArrayList<B>();
processElements(listB);

List<C> listC = new ArrayList<C>();
processElements(listC);
```

The `processElements()` method still cannot insert elements into the list, because you don't know if the list passed as parameter is typed to the class `A`, `B` or `C`.



## The super Wildcard Boundary

`List<? super A>` means that the list is typed to either the A class, or a superclass of A.

When you know that the list is typed to either A, or a superclass of A, it is safe to insert instances of A or subclasses of A (e.g. B or C) into the list. Here is an example:

```java
public static void insertElements(List<? super A> list){
    list.add(new A());
    list.add(new B());
    list.add(new C());
}
```

All of the elements inserted here are either A instances, or instances of A's superclass. Since both B and C extend A, if A had a superclass, B and C would also be instances of that superclass.

You can now call `insertElements()` with either a `List<A>`, or a `List` typed to a superclass of A. Thus, this example is now valid:

```java
List<A>      listA      = new ArrayList<A>();
insertElements(listA);

List<Object> listObject = new ArrayList<Object>();
insertElements(listObject);

```

The `insertElements()` method cannot read from the list though, except if it casts the read objects to `Object`. The elements already    present in the list when `insertElements()` is called could be of any type that is either an A or superclass    of A, but it is not possible to know exactly which class it is. However, since any class eventually subclass `Object`    you can read objects from the list if you cast them to `Object`.  Thus, this is valid:

```java
Object object = list.get(0);
```

But this is not valid:

```java
A object = list.get(0);
```





# References

[Java Generics Tutorial](http://tutorials.jenkov.com/java-generics/index.html)

[Java Generic Classes](http://tutorials.jenkov.com/java-generics/classes.html)

[Java Generic Methods](http://tutorials.jenkov.com/java-generics/methods.html)

[Java Generics - Class Objects as Type Literals](http://tutorials.jenkov.com/java-generics/class-objects-as-type-literals.html)

[Java Generic's Wildcards](http://tutorials.jenkov.com/java-generics/wildcards.html)

---

created at 2017-06-13 17:44