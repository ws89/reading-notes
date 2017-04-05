# Summary

![](http://www.javatpoint.com/images/core/final.jpg)



# final class

A class that is declared final cannot be subclassed. This is particularly useful, for example, when creating an immutable class like the `String` class.

``` java
public final class FinalClass {
}

public class FinalClassSub extends FinalClass {
  // Cannot inherit from final 'io.github.ws89.notes.java.FinalClass'
}
```



# final method

You might wish to make a method final if it has an implementation that should not be changed and it is critical to the consistent state of the object. 

Methods called from constructors should generally be declared final. If a
 constructor calls a non-final method, a subclass may redefine that 
method with surprising or undesirable results.

``` java
class ChessAlgorithm {
    enum ChessPlayer { WHITE, BLACK }
    ...
    final ChessPlayer getFirstPlayer() {
        return ChessPlayer.WHITE;
    }
    ...
}
```

A final method cannot be overridden or hidden by subclasses.

A final method is inherited but you cannot override it.

``` java
public  class FinalClass {
    final void m1(){
        System.out.println("supper class#m1");
    }
}

public class FinalClassSub  extends FinalClass  { 
  /* 'm1()' cannot override 'm1()' in 'io.github.ws89.notes.java.FinalClass'; overridden method is final */
  void m1(){ }
  
    @Test
    public void test(){
        super.m1();// output:supper class#m1
      	//can call super class's final method
    }
}
```



# final field



Cannot change the final variable reference.

``` java
@Test
public final void test(){
  final FinalClass finalClass = new FinalClass();
  finalClass = null; /* Cannot assign a value to final variable 'finalClass' */
}
```

the state of the object may be changed by operations on the object, but the variable will always refer to the same object.

``` java
/* √ */
public final class FinalClass {
    public String s ;
    @Test
    public final void test(){
        final FinalClass finalClass = new FinalClass();
        System.out.println(finalClass.s);//output:null
        finalClass.s = "aaa";
        System.out.println(finalClass.s);//output:aaa 
      	
      	final FinalClass finalClass1 = null; //right,can be null,but cannot be changed similarly.
    }
}
```



A final variable can only be initialized once, **either via an initializer or an assignment statement**.

a **blank final** static variable must be definitely assigned in a static initializer of the class in which it is declared.

``` java
public class Sphere {
    // pi is a universal constant, about as constant as anything can be.
    public static final double PI = 3.141592653589793;

    public final double radius;
    public final double xPos;
    public final double yPos;
    public final double zPos;

    Sphere(double x, double y, double z, double r) {
         radius = r;
         xPos = x;
         yPos = y;
         zPos = z;
    }
}
```

Since the obj variable goes out of scope with each iteration of the loop, it is actually redeclared each iteration, allowing the same token (i.e. obj) to be used to represent multiple variables.


```java
@Test
public void test2(){
  List<Integer> list = new ArrayList();
  list.add(1);
  list.add(2);

  for(final Integer obj : list){ /* √ */
    System.out.println(obj); 
    obj = 2; /* Variable 'obj' might already have been assigned to */
  }
}
```

# final and inner class

This allows the Java compiler to "capture" the value of the variable at run-time and store a copy as a field in the inner class. Once the outer method has terminated and its stack frame has been removed, the original variable is gone but the inner class's private copy persists in the class's own memory.

``` java
@Test
public void test3(){
  Integer i = 1;
  List<String> list = Arrays.asList("a"); //correct. if not change the reference,final is unnecessary!!!
  Executors.newFixedThreadPool(1).execute(new Runnable() {
    @Override
    public void run() {
      list.clear(); //correct
      System.out.println(list.size()); //correct
      
      //but below is wrong,complie error.[Variable 'list' is accessed from within inner class, needs to be declared final]
      // list = new ArrayList(); 
    }
  });
}
```



# Others

- There is no **final** interface in Java, as the purpose of interface is to be implemented.
- We cannot have a class as both **abstract** and **final**, because the purpose of an abstract class is to be inherited.
- cannot declare a constructor final,because constructor is never inherited.

# References

[Writing Final Classes and Methods](https://docs.oracle.com/javase/tutorial/java/IandI/final.html)

[Final_(Java)](https://en.wikipedia.org/wiki/Final_(Java))

[How Java final modifier affect your code](http://www.codejava.net/java-core/the-java-language/how-java-final-modifier-affect-your-code)

[Final Keyword In Java](http://www.javatpoint.com/final-keyword)

---

created at 2017-04-05 20:40