# Java Lambda Expressions

Java lambda expressions are new in Java 8. Java lambda expressions are Java's first step into functional programming. A Java lambda expression is thus a function which can be created without belonging to any class. A lambda expression can be passed around as if it was an object and executed on demand.

## Java Lambdas and the Single Method Interface

Functional programming is very often used to implement event listeners. Event listeners in Java are often    defined as Java interfaces with a single method. Here is a fictive single method interface example:

```java
public interface StateChangeListener {

    public void onStateChange(State oldState, State newState);

}
```

This Java interface defines a single method which is called whenever the state changes (in whatever is being observed).

​    In Java 7 you would have to implement this interface in order to listen for state changes. Imagine you have a class    called `StateOwner` which can register state event listeners. Here is an example:

```java
public class StateOwner {

    public void addStateListener(StateChangeListener listener) { ... }

}
```

​    In Java 7 you could add an event listener using an anonymous interface implementation, like this:

```java
StateOwner stateOwner = new StateOwner();

stateOwner.addStateListener(new StateChangeListener() {

    public void onStateChange(State oldState, State newState) {
        // do something with the old and new state.
    }
});
```

First a `StateOwner` instance is created. Then an anonymous implementation of the    `StateChangeListener` interface is added as listener on the `StateOwner` instance.

​    In Java 8 you can add an event listener using a Java lambda expression, like this:

```java
StateOwner stateOwner = new StateOwner();

stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed")
);
```

​    The lambda expressions is this part:

```java
(oldState, newState) -> System.out.println("State changed")
```

​    The lambda expression is matched against the parameter type of the `addStateListener()` method's parameter.    If the lambda expression matches the parameter type (in this case the `StateChangeListener` interface) , then    the lambda expression is turned into a function that implements the same interface as that parameter.

Java lambda expressions can only be used where the type they are matched against    is a single method interface.    In the example above, a lambda expression is used as parameter where the parameter type was the    `StateChangeListener` interface. This interface only has a single method. Thus, the    lambda expression is matched successfully against that interface.

### Matching Lambdas to Interfaces

A single method interface is also sometimes referred to as a *functional interface*.    Matching a Java lambda expression against a functional interface is divided into these steps:

- Does the interface have only one method?
- Does the parameters of the lambda expression match the parameters of the single method?
- Does the return type of the lambda expression match the return type of the single method?

If the answer is yes to these three questions, then the given lambda expression is matched    successfully against the interface.



## Lambda Type Inference

Before Java 8 you would have to specify what interface to implement, when making anonymous interface    implementations. Here is the anonymous interface implementation example from the beginning of this text:

```java
stateOwner.addStateListener(new StateChangeListener() {

    public void onStateChange(State oldState, State newState) {
        // do something with the old and new state.
    }
});
```

With lambda expressions the type can often be *inferred* from the surrounding code. For instance,    the interface type of the parameter can be inferred from the method declaration of the `addStateListener()`    method (the single method on the `StateChangeListener` interface). This is called *type inference*.     The compiler infers the type of a parameter by looking elsewhere    for the type - in this case the method definition. Here is the example from the beginning of this text,    showing that the `StateChangeListener` interface is not mentioned in the lambda expression:

```java
stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed")
);
```

In the lambda expression the parameter types can often be inferred too. In the example above, the compiler can infer their type from the    `onStateChange()` method declaration. Thus, the type of the parameters `oldState` and `newState`    are inferred from the method declaration of the `onStateChange()` method.



## Lambda Parameters

Since Java lambda expressions are effectively just methods, lambda expressions can take parameters just like methods.    The `(oldState, newState)` part of the lambda expression shown earlier specifies the parameters the lambda    expression takes. These parameters have to match the parameters of the method on the single method interface.    In this case, these parameters have to match the parameters of the `onStateChange()` method of    the `StateChangeListener` interface:

```java
public void onStateChange(State oldState, State newState);
```

As a minimum the number of parameters in the lambda expression and the method must match.

Second, if you have specified any parameter types in the lambda expression, these types must match too.    I haven't shown you how to put types on lambda expression parameters yet (it is shown later in this text),    but in many cases you don't need them.



### Zero Parameters

​    If the method you are matching your lambda expression against takes no parameters, then you can write    your lambda expression like this:

```java
() -> System.out.println("Zero parameter lambda");
```

Notice how the parentheses have no content in between. That is to signal that the lambda takes    no parameters.



### One Parameter

If the method you are matching your Java lambda expression against takes one parameter, you can write    the lambda expression like this:

```java
(param) -> System.out.println("One parameter: " + param);
```

Notice the parameter is listed inside the parentheses.

When a lambda expression takes a single parameter, you can also omit the parentheses, like this:

```java
 param -> System.out.println("One parameter: " + param);
```



### Multiple Parameters

If the method you match your Java lambda expression against takes multiple parameters, the parameters    need to be listed inside parentheses.    Here is how that looks in Java code:

```java
(p1, p2) -> System.out.println("Multiple parameters: " + p1 + ", " + p2);
```

Only when the method takes a single parameter can the parentheses be omitted.



### Parameter Types

Specifying parameter types for a lambda expression may sometimes be necessary if the compiler cannot    infer the parameter types from the functional interface method the lambda is matching. Don't worry,    the compiler will tell you when that is the case. Here is a Java lambda parameter type example:

```java
(Car car) -> System.out.println("The car is: " + car.getName());
```

As you can see, the type (`Car`) of the `car` parameter is written in front of the parameter name itself, just like    you would when declaring a parameter in a method elsewhere, or when making an anonymous implementation of an interface.



## Lambda Function Body

​    The body of a lambda expression, and thus the body of the function / method it represents, is specified    to the right of the `->` in the lambda declaration: Here is an example:

```java
 (oldState, newState) -> System.out.println("State changed")
```

If your lambda expression needs to consist of multiple lines, you can enclose the lambda function body inside    the `{  }` bracket which Java also requires when declaring methods elsewhere. Here is an example:

```java
 (oldState, newState) -> {
    System.out.println("Old state: " + oldState);
    System.out.println("New state: " + newState);
  }
```



## Returning a Value From a Lambda Expression

You can return values from Java lambda expressions, just like you can from a method. You just add    a return statement to the lambda function body, like this:

```java
 (param) -> {
    System.out.println("param: " + param);
    return "return value";
  }
```

In case all your lambda expression is doing is to calculate a return value and return it, you can    specify the return value in a shorter way. Instead of this:

```java
 (a1, a2) -> { return a1 > a2; }
```

You can write:

```java
 (a1, a2) -> a1 > a2;
```

The compiler then figures out that the expression `a1 > a2` is the return value of    the lambda expression (hence the name lambda *expressions* - as expressions return a value of some kind).



## Lambdas as Objects

A Java lambda expression is essentially an object. You can assign a lambda expression to a variable    and pass it around, like you do with any other object. Here is an example:

```java
public interface MyComparator {

    public boolean compare(int a1, int a2);

}

MyComparator myComparator = (a1, a2) -> return a1 > a2;

boolean result = myComparator.compare(2, 5);
```

The first code block shows the interface which the lambda expression implements.    The second code block shows the definition of the lambda expression, how the lambda    expression is assigned to variable, and finally how the lambda expression is invoked    by invoking the interface method it implements.



# Practice

```java
/**
 * Created by WS on 2017/6/15.
 */
public class LambdaTest {

    @Test
    public void test(){

        StateOwner stateOwner = new StateOwner("1","2");

        stateOwner.addStateListener(new StateChangeListener() {
            @Override
            public void onStateChange(String oldState, String newState) {
                System.out.println( oldState + " to " + newState);//1 to 2
            }
        });

        stateOwner.addStateListener(
                (oldState,newState) -> System.out.println( oldState + " to " + newState )//1 to 2
        );

        stateOwner.addStateListener(
                (oldState,newState) -> {
                    System.out.println("oldState:" + oldState);//oldState:1
                    System.out.println("newState:" + newState);//newState:2
                }
        );

        String returnValue = stateOwner.testReturnValue(
                param -> {
                    System.out.println("test return method:" + param);//test return method:1
                    return "return:" + param;
                }
        );
        System.out.println( returnValue );//return:1

    }

    @Test
    public void asObject(){
        MyComparator myComparator = new MyComparator() {
            @Override
            public boolean compare(int a, int b) {
                return a>b;
            }
        };

        boolean result = myComparator.compare(1,2);
        System.out.println( result );//false

        myComparator = (a,b) -> a>b;
        result = myComparator.compare(2,1);
        System.out.println( result );//true
    }

}

interface StateChangeListener {
    void onStateChange(String oldState,String newState);
}

interface ReturnValue{
    String returnMethod(String param);
}

class StateOwner {
    String oldState = null;
    String newState = null;

    public StateOwner(String oldState, String newState) {
        this.oldState = oldState;
        this.newState = newState;
    }

    void addStateListener(StateChangeListener stateChangeListener){
        stateChangeListener.onStateChange( this.oldState , this.newState );
    }

    String testReturnValue(ReturnValue returnValue){
        return returnValue.returnMethod(oldState);
    }
}

interface MyComparator{
    boolean compare(int a,int b);
}
```





# Reference

[Java Lambda Expressions](http://tutorials.jenkov.com/java/lambda-expressions.html)



---

created at 2017-06-15 19:55