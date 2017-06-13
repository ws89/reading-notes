# AtomicReference

The `AtomicReference` class provides an object reference variable which can be read and written atomically.By atomic is meant that multiple threads attempting to change the same `AtomicReference` (e.g. with a compare-and-swap operation) will not make the `AtomicReference` end up in an inconsistent state. `AtomicReference` even has an advanced `compareAndSet()` method which lets you compare the reference to an expected value (reference) and if they are equal, set a new reference inside the `AtomicReference` object.



## Creating an AtomicReference

​    You can create an `AtomicReference` instance like this:

```java
AtomicReference atomicReference = new AtomicReference();
```

​    If you need to create the `AtomicReference` with an initial reference, you can do so like this:

```java
String initialReference = "the initially referenced string";
AtomicReference atomicReference = new AtomicReference(initialReference);
```



### Creating a Typed AtomicReference

You can use Java generics to create a typed `AtomicReference`. Here is a typed  `AtomicReference` example:

```java
AtomicReference<String> atomicStringReference = new AtomicReference<String>();
```

You can also set an initial value for a typed `AtomicReference`. Here is a typed `AtomicReference` instantiation example with an initial value:

```java
String initialReference = "the initially referenced string";
AtomicReference<String> atomicStringReference = new AtomicReference<String>(initialReference);
```



## Getting the AtomicReference Reference

You can get the reference stored in an `AtomicReference` using the `AtomicReference`'s    `get()` method. If you have an untyped `AtomicReference` then the `get()` method returns an `Object` reference. If you have a typed `AtomicReference` then `get()` returns    a reference to the type you declared on the `AtomicReference` variable when you created it.

Here is first an untyped `AtomicReference` `get()` example:

```java
AtomicReference atomicReference = new AtomicReference("first value referenced");
String reference = (String) atomicReference.get();
```

Notice how it is necessary to cast the reference returned by `get()` to a `String` because  `get()` returns an `Object` reference when the `AtomicReference` is untyped.

Here is a typed `AtomicReference` example:

```java
AtomicReference<String> atomicReference = new AtomicReference<String>("first value referenced");
String reference = atomicReference.get();
```

Notice how it is no longer necessary to cast the referenced returned by `get()` because the compiler knows it will return a `String` reference.



## Setting the AtomicReference Reference

You can set the reference stored in an `AtomicReference` instance using its `set()` method. In an untyped `AtomicReference` instance the `set()` method takes an `Object` reference as parameter. In a typed `AtomicReference` the `set()` method takes whatever type as parameter you declared as its type when you declared the `AtomicReference`.

Here is an `AtomicReference` `set()` example:

```java
AtomicReference atomicReference = new AtomicReference();    
atomicReference.set("New object referenced");
```

There is no difference to see in the use of the `set()` method for an untyped or typed reference. The only real difference you will experience is that the compiler will restrict the types you can set on a typed  `AtomicReference`.



## Comparing and Setting the AtomicReference Reference

The `AtomicReference` class contains a useful method named `compareAndSet()`. The    `compareAndSet()` method can compare the reference stored in the `AtomicReference`  instance    with an expected reference, and if they two references are the same (not equal as in `equals()` but    same as in `==`), then a new reference can be set on the `AtomicReference` instance.

If `compareAndSet()` sets a new reference in the `AtomicReference` the `compareAndSet()`    method returns `true`. Otherwise `compareAndSet()` returns `false`.

Here is an `AtomicReference` `compareAndSet()` example:

```java
String initialReference = "initial value referenced";

AtomicReference<String> atomicStringReference = new AtomicReference<String>(initialReference);

String newReference = "new value referenced";
boolean exchanged = atomicStringReference.compareAndSet(initialReference, newReference);
System.out.println("exchanged: " + exchanged);

exchanged = atomicStringReference.compareAndSet(initialReference, newReference);
System.out.println("exchanged: " + exchanged);
```

This example creates a typed `AtomicReference` with an initial reference. Then it calls `comparesAndSet()`  two times to compare the stored reference to the initial reference, and set a new reference if the stored reference is equal to the initial reference. The first time the two references are the same, so a new reference is set on the  `AtomicReference`. The second time the stored reference is the new reference just set in the call to `compareAndSet()` before, so the stored reference is of course not equal to the initial reference. Thus, a new reference is not set on the `AtomicReference` and the `compareAndSet()` method returns `false`.



# AtomicStampedReference

The `AtomicStampedReference` is different from the [`AtomicReference`](http://tutorials.jenkov.com/java-util-concurrent/atomicreference.html) in that the `AtomicStampedReference` keeps both an object reference and a stamp internally. The reference and stamp can be swapped using a single atomic compare-and-swap operation, via the `compareAndSet()`    method.

The `AtomicStampedReference` is designed to be able to solve the A-B-A problem which is not possible to solve with an `AtomicReference` alone. 



## Creating an AtomicStampedReference

```java
Object initialRef   = null;
int    initialStamp = 0;

AtomicStampedReference atomicStampedReference = new AtomicStampedReference(intialRef, initialStamp);
```

Here is a typed AtomicStampedReference example: 

```java
String initialRef   = null;
int    initialStamp = 0;

AtomicStampedReference<String> atomicStampedStringReference = new AtomicStampedReference<String>( initialRef, initialStamp );
```

## Getting the AtomicStampedReference Reference

```java
String initialRef = "first text";

AtomicStampedReference<String> atomicStampedReference =
    new AtomicStampedReference<String>(
        initialRef, 0
    );

String reference = atomicStampedReference.getReference();
```



## Getting the AtomicStampedReference Stamp

The `AtomicStampedReference` also contains a `getStamp()` method which can be used to    obtain the internally stored stamp. Here is a `getStamp()` example:

```java
String initialRef = "first text";

AtomicStampedReference atomicStampedReference =vnew AtomicStampedReference(initialRef, 0);

int stamp = atomicStampedReference.getStamp();
```



## Getting Reference and Stamp Atomically

You can obtain both reference and stamp from an `AtomicStampedReference` in a single, atomic operation using the `get()` method. The `get()` method returns the reference as return value from the method. The stamp is inserted into an `int[]` array that is passed as parameter to the `get()` method. Here is a `get()` example:

```java
String initialRef   = "text";
String initialStamp = 0;

AtomicStampedReference atomicStampedReference =
    new AtomicStampedReference(
        initialRef, initialStamp
    );

int[] stampHolder = new int[1];
Object ref = atomicStampedReference.get(stampHolder);

System.out.println("ref = " + ref);
System.out.println("stamp = " + stampHolder[0]);
```

**Being able to obtain both reference and stamp as a single atomic operation** is important for some types of concurrent algorithms.



## Setting the AtomicStampedReference Reference

```java
AtomicStampedReference atomicStampedReference = new AtomicStampedReference(null, 0);
String newRef = "New object referenced";
int    newStamp = 1;

atomicStampedReference.set(newRef, newStamp);
```



## Comparing and Setting the AtomicStampedReference Reference

The `AtomicStampedReference` class contains a useful method named `compareAndSet()`. The    `compareAndSet()` method can compare the reference stored in the `AtomicStampedReference`  instance    with an expected reference, and the stored stamp with an expected stamp, and if they two references and stamps are    the same (not equal as in `equals()` but same as in `==`), then a new reference can be    set on the `AtomicStampedReference` instance.

If `compareAndSet()` sets a new reference in the `AtomicStampedReference` the `compareAndSet()`    method returns `true`. Otherwise `compareAndSet()` returns `false`.

```java
String initialRef   = "initial value referenced";
int    initialStamp = 0;

AtomicStampedReference<String> atomicStringReference =new AtomicStampedReference<String>( initialRef, initialStamp );

String newRef   = "new value referenced";
int    newStamp = initialStamp + 1;

boolean exchanged = atomicStringReference.compareAndSet(initialRef, newRef, initialStamp, newStamp);
System.out.println("exchanged: " + exchanged);  //true

exchanged = atomicStringReference.compareAndSet(initialRef, "new string", newStamp, newStamp + 1);
System.out.println("exchanged: " + exchanged);  //false

exchanged = atomicStringReference.compareAndSet(newRef, "new string", initialStamp, newStamp + 1);
System.out.println("exchanged: " + exchanged);  //false

exchanged = atomicStringReference.compareAndSet(newRef, "new string", newStamp, newStamp + 1);
System.out.println("exchanged: " + exchanged);  //true
```



## AtomicStampedReference and the A-B-A Problem

The `AtomicStampedReference` is designed to solve the A-B-A problem. The A-B-A problem is when a reference is changed from pointing to A, then to B and then back to A.

By using an `AtomicStampedReference` instead of an `AtomicReference` it is possible to    detect the A-B-A situation. Thread 1 can copy the reference and stamp out of the `AtomicStampedReference`    atomically using `get()`. If another thread changes the reference from A to B and then back to A, then    the stamp will have changed (provided threads update the stamp sensibly - e.g increment it).

The code below shows how to detect the A-B-A situation using the `AtomicStampedReference`:

```java
int[] stampHolder = new int[1];
Object ref = atomicStampedReference.get(stampHolder);

if(ref == null){
    //prepare optimistic modification
}

//if another thread changes the reference and stamp here,
//it can be detected

int[] stampHolder2 = new int[1];
Object ref2 = atomicStampedReference.get(stampHolder);

if(ref == ref2 && stampHolder[0] == stampHolder2[0]){
    //no modification since optimistic modification was prepared

} else {
    //retry from scratch.
}
```



# References

[AtomicReference](http://tutorials.jenkov.com/java-util-concurrent/atomicreference.html)

[AtomicStampedReference](http://tutorials.jenkov.com/java-util-concurrent/atomicstampedreference.html)

---

created at 2017-06-13 16:14