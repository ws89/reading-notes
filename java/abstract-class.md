# Java Abstract Classes

A *Java abstract class* is a class which cannot be instantiated, meaning you cannot create new instances of an abstract class. The purpose of an abstract class is to function as a base for subclasses. 



## Declaring an Abstract Class in Java

```java
public abstract class MyAbstractClass {

}
```



## Abstract Methods

```java
public abstract class MyAbstractClass {

    public abstract void abstractMethod();
}
```



## The Purpose of Abstract Classes

The purpose of abstract classes is to function as base classes which can be extended by subclasses    to create a full implementation. For instance, imagine that a certain process requires 3 steps:

1. The step before the action.
2. The action.
3. The step after the action.

If the steps before and after the action are always the same, the 3-step process could be implemented in an abstract superclass with this Java code:

```java
public abstract class MyAbstractProcess {

    public void process() {
        stepBefore();
        action();
        stepAfter();
    }

    public void stepBefore() {
        //implementation directly in abstract superclass
    }

    public abstract void action(); // implemented by subclasses

    public void stepAfter() {
        //implementation directly in abstract superclass
    }
}
```

​    Notice how the `action()` method is abstract. Subclasses of `MyAbstractProcess`    can now extend `MyAbstractProcess` and just override the `action()` method.

When the `process()` method of the subclass is called, the full process is executed, including the `stepBefore()` and `stepAfter()` of the abstract superclass, and the `action()` method of the subclass.

Of course, the `MyAbstractProcess` did not have to be an abstract class to function as a base    class. Nor did the `action()` method have to be abstract either. You could have just used an    ordinary class. However, by making the method to implement abstract, and thus the class too, **you signal clearly to users of this class that this class should not be used as it is.** Instead it should be used as a base class for a subclass, and that the abstract method should be implemented in the subclass.

The above example did not have a default implementation for the `action()` method. In some cases your superclass might actually have a default implementation for the method that subclasses are supposed to override. In that case, you cannot make the method abstract. You can still make the superclass abstract though, even if it contains no abstract methods.

​    Here is a more concrete example that opens a URL, processes it and closes the connection to the URL afterwards.

```java
public abstract class URLProcessorBase {

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

Notice how the `processURLData()` is an abstract method, and that `URLProcessorBase` is an abstract class. Subclasses of `URLProcessorBase` have to implement the `processURLData()` method because it is an abstract method.

Subclasses of `URLProcessorBase` abstract class can process data downloaded from URLs without worrying about opening and closing the network connection to the URL. This is done by the `URLProcessorBase`.    Subclasses only need to worry about processing the data from the `InputStream` passed to the `processURLData()` method. This makes it easier to implement classes that processes data from URLs.

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

Notice how the subclass only implements the `processURLData()` method, and nothing more. The rest of the code is inherited from the `URLProcessorBase` superclass.

Here is an example of how to use the `URLProcessorImpl` class:

```java
URLProcessorImpl urlProcessor = new URLProcessorImpl();

urlProcessor.process(new URL("http://jenkov.com"));
```

The `process()` method is called, which is implemented in the `URLProcessorBase`    superclass. This method in turn calls the `processURLData()` in the `URLProcessorImpl` class.

### Abstract Classes and the Template Method Design Pattern

The example I showed you above with the `URLProcessorBase` class is actually an example of the **Template Method design pattern**. The Template Method design pattern provides a partial implementation of some process, which subclasses can complete when extending the Template Method base class.



# References

[Java Abstract Classes](http://tutorials.jenkov.com/java/abstract-classes.html)



---

created at 2017-06-18 22:42