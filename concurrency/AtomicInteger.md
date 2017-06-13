# AtomicInteger

The `AtomicInteger` class provides you with a `int` variable which can be read and written atomically, and which also contains advanced atomic operations like `compareAndSet()`. The `AtomicInteger` class is located in the `java.util.concurrent.atomic` package, so the full class name is
 `java.util.concurrent.atomic.AtomicInteger` . 



## Creating an AtomicInteger

```java
AtomicInteger atomicInteger = new AtomicInteger();
```

This example creates an `AtomicInteger` with the initial value `0` .

If you want to create an `AtomicInteger` with an initial value, you can do so like this:

```java
AtomicInteger atomicInteger = new AtomicInteger(123);
```

This example passes a value of `123` as parameter to the `AtomicInteger` contructor,    which sets the initial value of the `AtomicInteger` instance to `123` .

## Getting the AtomicInteger Value

You can get the value of an `AtomicInteger` instance via the `get()` method.    Here is an `AtomicInteger.get()` example:

```java
AtomicInteger atomicInteger = new AtomicInteger(123);

int theValue = atomicInteger.get();
```

## Setting the AtomicInteger Value

You can set the value of an `AtomicInteger` instance via the `set()` method.    Here is an `AtomicInteger.set()` example:

```
AtomicInteger atomicInteger = new AtomicInteger(123);

atomicInteger.set(234);

```

​    This example creates an `AtomicInteger` example with an initial value of 123, and then sets its    value to `234` in the next line.

## Compare and Set the AtomicInteger Value

The `AtomicInteger` class also has an atomic `compareAndSet()` method. This method    compares the current value of the `AtomicInteger` instance to an expected value, and if the two    values are equal, sets a new value for the `AtomicInteger` instance. Here is an    `AtomicInteger.compareAndSet()` example:

```
AtomicInteger atomicInteger = new AtomicInteger(123);

int expectedValue = 123;
int newValue      = 234;
atomicInteger.compareAndSet(expectedValue, newValue);

```

This example first creates an `AtomicInteger` instance with an initial value of `123` .    Then it compares the value of the `AtomicInteger` to the expected value `123` and    if they are equal the new value of the `AtomicInteger` becomes `234`;

## Adding to the AtomicInteger Value

The `AtomicInteger` class contains a few methods you can use to add a value to the `AtomicInteger`    and get its value returned. These methods are:

- `addAndGet()`
- `getAndAdd()`
- `getAndIncrement()`
- `incrementAndGet()`

The first method, `addAndGet()` adds a number to the `AtomicInteger` and returns its value    after the addition. The second method, `getAndAdd()` also adds a number to the `AtomicInteger`    but returns the value the `AtomicInteger` had before the value was added. Which of these two methods    you should use depends on your use case. Here are two examples:

```
AtomicInteger atomicInteger = new AtomicInteger();


System.out.println(atomicInteger.getAndAdd(10));
System.out.println(atomicInteger.addAndGet(10));

```

This example will print out the values `0` and `20`. First the example gets the value    of the `AtomicInteger` before adding 10 to. Its value before addition is 0. Then the example    adds 10 to the `AtomicInteger` and gets the value after the addition. The value is now 20.

You can also add negative numbers to the `AtomicInteger` via these two methods. The result is effectively a subtraction.

The methods `getAndIncrement()` and `incrementAndGet()` works like `getAndAdd()` and `addAndGet()` but just add 1 to the value of the `AtomicInteger`.

## Subtracting From the AtomicInteger Value

The `AtomicInteger` class also contains a few methods for subtracting values from the `AtomicInteger`    value atomically. These methods are:

- `decrementAndGet()`
- `getAndDecrement()`

The `decrementAndGet()` subtracts 1 from the `AtomicInteger` value and returns its value    after the subtraction. The `getAndDecrement()` also subtracts 1 from the `AtomicInteger` value    but returns the value the `AtomicInteger` had before the subtraction.



# References

[AtomicInteger](http://tutorials.jenkov.com/java-util-concurrent/atomicinteger.html)



---

created at 2017-06-13 16:04