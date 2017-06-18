# Java Interfaces vs. Abstract Classes

**Java interfaces are used to decouple the interface of some component from the implementation.** In other words, to make the classes using the interface independent of the classes implementing    the interface. Thus, you can exchange the implementation of the interface, without having to    change the class using the interface.

**Abstract classes are typically used as base classes for extension by subclasses.** Some programming languages use abstract classes to achieve polymorphism, and to separate interface from implementation,    but in Java you use interfaces for that. Remember, a Java class can only have 1 superclass, but it    can implement multiple interfaces. Thus, if a class already    has a different superclass, it can implement an interface, but it cannot extend another abstract class.    Therefore interfaces are a more flexible mechanism for exposing a common interface.

**If you need to separate an interface from its implementation, use an interface. If you also need to provide a base class or default implementation of the interface, add an abstract class (or normal class) that implements the interface.**



Here is an example showing a class referencing an interface, an abstract class implementing that interface, and a subclass extending the abstract class.

| ![The blue class knows only the interface. The abstract class implements the interface, and the subclass inherits from the abstract class.](http://tutorials.jenkov.com/images/java/interfaces-vs-abstract-classes-1.png) |
| ---------------------------------------- |
| **The blue class knows only the interface. The abstract class implements the interface, and the subclass inherits from the abstract class.** |



First the interface:

```java
public interface URLProcessor {

    public void process(URL url) throws IOException;
}
```

Second, the abstract base class:

```java
public abstract class URLProcessorBase implements URLProcessor {

    public void process(URL url) throws IOException {
        URLConnection urlConnection = url.openConnection();
        InputStream input = urlConnection.getInputStream();

        try{
            processURLData(input);
        } finally {
            input.close();
        }
    }

    protected abstract void processURLData(InputStream input)
        throws IOException;

}
```

Third, the subclass of the abstract base class:

```java
public class URLProcessorImpl extends URLProcessorBase {

    @Override
    protected void processURLData(InputStream input) throws IOException {
        int data = input.read();
        while(data != -1){
            System.out.println((char) data);
            data = input.read();
        }
    }
}
```

Fourth, how to use the interface `URLProcessor` as variable type, even though it is the subclass `UrlProcessorImpl` that is instantiated.

```java
URLProcessor urlProcessor = new URLProcessorImpl();

urlProcessor.process(new URL("http://jenkov.com"));
```

Using both an interface and an abstract base class makes your code more flexible. It possible to implement simple URL processors simply by subclassing the abstract base class. If you need something more advanced, your URL processor can just implement the `URLProcessor` interface directly, and not inherit from `URLProcessorBase`.



# References

[Java Interfaces vs. Abstract Classes](http://tutorials.jenkov.com/java/interfaces-vs-abstract-classes.html)



---

created at 2017-06-18 23:37