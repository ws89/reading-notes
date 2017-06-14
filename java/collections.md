# Java Collections - hashCode() and equals()

 The methods `hashCode()` and  `equals()` play a distinct role in the objects you insert into Java collections. The specific contract rules of these two methods are best described in the JavaDoc. Here I will just tell you what role they play. What they are used for, so you know why their implementations are important.

## equals()

 `equals()` is used in most collections to determine if a collection contains a given element. For instance:

```java
List list = new ArrayList();
list.add("123");

boolean contains123 = list.contains("123");
```

 The `ArrayList` iterates all its elements and execute `"123".equals(element)` to determine if the element is equal to the parameter object "123". It is the `String.equals()` implementation that determines if two strings are equal.

 The `equals()` method is also used when removing elements. For instance:

```java
List list = new ArrayList();
list.add("123");

boolean removed = list.remove("123");
```

 The `ArrayList` again iterates all its elements and execute `"123".equals(element)` to determine if the element is equal to the parameter object "123". The first element it finds that is equal to the given parameter "123" is removed.

 As you can see, a proper implementation of `.equals()` is essential for your own classes to work well with the Java Collection classes. So how do you implement `equals()` "properly"?

 So, when are two objects equal? That depends on your application, the classes, and what you are trying to do. For instance, let's say you are loading and processing `Employee` objects stored in a database. Here is a simple example of such an `Employee` class:

```java
public class Employee {
    protected long   employeeId;
    protected String firstName;
    protected String lastName;
}
```

 You could decide that two `Employee` objects are equal to each other if just their `employeeId`'s are equal. Or, you could decide that all fields must be equal - both `employeeId`, `firstName` and `lastName`. Here are two example implementation of `equals()` matching these criterias:

```java
public class Employee {
  ...
  public boolean equals(Object o){
    if(o == null)                return false;
    if(!(o instanceof) Employee) return false;

    Employee other = (Employee) o;
    return this.employeeId == other.employeeId;
  }
}
```

```java
public class Employee {
  ...
  public boolean equals(Object o){
    if(o == null)                return false;
    if(!(o instanceof) Employee) return false;

    Employee other = (Employee) o;
    if(this.employeeId != other.employeeId)      return false;
    if(! this.firstName.equals(other.firstName)) return false;
    if(! this.lastName.equals(other.lastName))   return false;

    return true;
  }
}
```

 Which of these two implementations is "proper" depends on what you need to do. Sometimes you need to lookup an `Employee` object from a cache. In that case perhaps all you need is for the `employeeId` to be equal. In other cases you may need more than that - for instance to determine if a copy of an `Employee` object has changed from the original.

## hashCode()

 The `hashCode()` method of objects is used when you insert them into a `HashTable`, `HashMap` or `HashSet`. If you do not know the theory of how a hashtable works internally, you can read about [hastables on Wikipedia.org](http://en.wikipedia.org/wiki/Hashtable).

 When inserting an object into a hastable you use a key. The hash code of this key is calculated, and used to determine where to **store** the object internally. When you need to lookup an object in a hashtable you also use a key. The hash code of this key is calculated and used to determine where to **search** for the object.

**The hash code only points to a certain "area" (or list, bucket etc) internally. Since different key objects could potentially have the same hash code, the hash code itself is no guarantee that the right key is found. The hashtable then iterates this area (all keys with the same hash code) and uses the key's `equals()` method to find the right key. Once the right key is found, the object stored for that key is returned.**

 So, as you can see, a combination of the `hashCode()` and `equals()` methods are used when storing and when looking up objects in a hashtable.

Here are two rules that are good to know about implementing the `hashCode()` method in your own classes, if the hashtables in the Java Collections API are to work correctly:

1. **If object1 and object2 are equal according to their `equals()` method, they must also have the same hash code.**
2. **If object1 and object2 have the same hash code, they do NOT have to be equal too.**

 In shorter words:

1. If equal, then same hash codes too.
2. Same hash codes no guarantee of being equal.

 Here are two example implementation of the `hashCode()` method matching the `equals()` methods shown earlier:

```java
public class Employee {
  protected long   employeeId;
  protected String firstName;
  protected String lastName;

  public int hashCode(){
    return (int) employeeId;
  }
}
```

```java
public class Employee {
    protected long   employeeId;
    protected String firstName;
    protected String lastName;

  public int hashCode(){
    return (int) employeeId *
                firstName.hashCode() *
                lastName.hashCode();
  }
}
```

Notice, that if two `Employee` objects are equal, they will also have the same hash code. But, as is especially easy to see in the first example, two `Employee` objects can be not equal, and still have the same hash code.

In both examples the hash code is the `employeeId` is rounded down to an `int`. That means that many employee id's could result in the same hash code, but these `Employee` objects would still not be equal, since they don't have the same employee id.



# Java Collections - Streams

Java 8 Streams is a new addition to the Java Collections API, which brings a new way to process collections of objects. Thus, streams in the Java Collections API is a different concept than the input and output streams  in the Java IO API, even if the idea is similar (a stream of objects from a collection, instead of a stream of bytes or characters).



## Obtaining a Stream From a Collection

```java
List<String> items = new ArrayList<String>();

items.add("one");
items.add("two");
items.add("three");

Stream<String> stream = items.stream();
```

First a `List` of strings is created and three strings are added to it.    Then a `Stream` of strings is obtained by calling the `items.stream()`    method. This is similar to how  you obtain an `Iterator` by calling the `items.iterator()`    method, but a `Stream` is a different animal than an `Iterator`.



## Stream Processing Phases

Once you have obtained a `Stream` instance from a `Collection` instance, you use that    stream to process the elements in the collection.

Processing the elements in the stream happens in two steps / phases:

1. Configuration
2. Processing

First the stream is configured. The configuration can consist of filters and mappings.    Second, the stream is processed. The processing consists of doing something to the    filtered and mapped objects. No processing takes place during the configuring calls.    Not until a processing method is called on the stream.



## Stream.filter()

```java
stream.filter( item -> item.startsWith("o") );
```

The `filter()` method takes a `Predicate` as parameter.    The `Predicate` interface contains a function called `test()` which    the [lambda expression](http://tutorials.jenkov.com/java/lambda-expressions.html) passed as parameter above    is matched against. In other words, the lambda expression implements the `Predicate.test()`    method.

The `test()` method is defined like this:

```
boolean test(T t)

```

It takes a single parameter and returns a `boolean`. If you look at the lambda expression above,    you can see that it takes a single parameter `item` and returns a boolean - the result of    the `item.startsWith("o")` method call.

When you call the `filter()` method on a `Stream`, the filter passed as parameter to the `filter()` method is stored internally. **No filtering takes place yet.**

The parameter passed to the `filter()` function determines what items in the stream should be processed, and which that should be excluded from the processing. If the `Predicate.test()`    method of the parameter passed to `filter()` returns `true` for an item, that    means it should be processed. If `false` is returned, the item is not processed.



## Stream.map()

It is possible to map the items in a collection to other objects. In other words, for each item in    the collection you create a new object based on that item. How the mapping is done is up to you.    Here is a simple Java stream mapping example:

```java
items.stream()
     .map( item -> item.toUpperCase() )
```

This example maps all strings in the `items` collection to their uppercase equivalents.

**Again, this example doesn't actually perform the mapping.** It only configures the stream for mapping. Once one of the stream processing methods are invoked, the mapping (and filtering) will be performed.



## Stream.collect()

The `collect()` method is one of the stream **processing methods** on the `Stream` interface. When this method is invoked, the filtering and mapping will take place and the object resulting from those actions will be collected. Here is a `stream.collect()` example:

```java
List<String> filtered = items.stream()
    .filter( item -> item.startsWith("o") )
    .collect(Collectors.toList());
```

This example creates a stream, adds a filter, and collects all object accepted by the filter    in a `List`. The filter only accepts items (strings) which start with the character    `o`. The resulting `List` thus contains all strings from the `items`    collection which starts with the character `o`. 



## Stream.min() and Stream.max()

The `min()` and `max()` methods are stream processing methods. Once these are called,    the stream will be iterated, filtering and mapping applied, and the minimum or maximum value in the stream    will be returned.

Here is a Java `Stream.min()` example:

```java
 String shortest = items.stream()
        .min(Comparator.comparing(item -> item.length()))
        .get();
```

The `min()` and `max()` methods return an `Optional` instance which has a `get()` method on, which you use to obtain the value. In case the stream has no elements the `get()` method will return null.

The `min()` and `max()` methods take a `Comparator` as parameter. The `Comparator.comparing()` method creates a `Comparator` based on the    lambda expression passed to it. In fact, the `comparing()` method takes a `Function` which is a functional interface suited for lambda expressions. It takes one parameter and returns a value.



## Stream.count()

The `count()` method simply returns the number of elements in the stream after filtering has been applied. Here is an example:

```java
long count = items.stream()
     .filter( item -> item.startsWith("t"))
     .count();
```

This example iterates the stream and keeps all elements that start with the character `t`, and then counts these elements.

The `count()` method returns a `long` which is the count of elements in the stream after filtering etc.



## Stream.reduce()

The `reduce()` method can reduce the elements of a stream to a single value.    Here is an example:

```java
String reduced2 = items.stream()
        .reduce((acc, item) -> acc + " " + item)
        .get();
```

The `reduce()` method takes a `BinaryOperator` as parameter, which can easily    be implemented using a lambda expression. The `BinaryOperator.apply()` method is the    method implemented by the lambda expression above. This method takes two parameters. The `acc`    which is the accumulated value, and `item` which is an element from the stream. Thus,    the value created by the `reduce()` function is the accumulated value after processing    the last element in the stream. In the example above, each item is concatenated to the accumulated    value. This is done by the lambda expression implementing the `BinaryOperator`.

The `reduce()` method taking a `BinaryOperator` as parameter returns an    `Optional` . In case the stream contains no elements, the `Optional.get()`    returns null. Otherwise it returns the reduced value.

There is another `reduce()` method which takes two parameters. It takes an initial value    for the accumulated value, and then a `BinaryOperator`. Here is an example:

```java
String reduced = items.stream()
        .reduce("", (acc, item) -> acc + " " + item);
```

This example takes an empty string as initial value, and then the same lambda expression as the    previous example. This version of the `reduce()` method returns the accumulated value    directly, and not an `Optional`. If the stream contains no elements, the initial value    will be returned.

The `reduce()` method can be combined with the `filter()` method too.    Here is an example:

```java
String reduced = items.stream()
    .filter( item -> item.startsWith("o"))
    .reduce("", (acc, item) -> acc + " " + item);
```

This example keeps all elements that start with the character `o`, and then reduce these    elements into a single value.



# Practice

```java
@Test
public void streams(){
    List<String> list = new ArrayList<>();
    list.add("one");
    list.add("two");
    list.add("three");

    List<String> list2 = list.stream()
                                .filter(item -> item.startsWith("o"))
                                .collect(Collectors.toList());

    for(String item : list2){
        System.out.println( item );// one
    }


    list2 = list2.stream().map(item -> item.toUpperCase())
                    .collect(Collectors.toList());

    for(String item : list2){
        System.out.println( item );// ONE
    }

    System.out.println( list.size() );// 3

    String shortest = list.stream().min(Comparator.comparing(item -> item.length()))
                .get();
    System.out.println( shortest );// one

    long count = list.stream().filter( item -> item.startsWith("t") ).count();
    System.out.println( count );// 2


    String reduced = list.stream()
                        .reduce( (accumulation,item) -> accumulation + "," + item )
                        .get();
    System.out.println( reduced );// one,two,three

    reduced = list.stream()
                    .filter( item -> item.startsWith("t"))
                    .reduce( (accumulation,item) -> accumulation + " " + item )
                    .get();
    System.out.println( reduced );// two three

    reduced = list.stream()
                    .reduce( "accumulations:",(accumulation,item) -> accumulation + "," + item );
    System.out.println( reduced );// accumulations:,one,two,three
}
```



# References

[Java Collections - hashCode() and equals()](http://tutorials.jenkov.com/java-collections/hashcode-equals.html)

[Java Collections - Streams](http://tutorials.jenkov.com/java-collections/streams.html)

---

created at 2017-06-14 21:00