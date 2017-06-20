# Java Annotations

Java annotations are used to provide meta data for your Java code. Being meta data, Java annotations do not directly affect the execution of your code, although some types of annotations can actually be used for that purpose.

Java annotations were added to Java from Java 5.



## Java Annotation Purposes

Java annotations are typically used for the following purposes:

- Compiler instructions
- Build-time instructions
- Runtime instructions

Java annotations can be be used at build-time, when you build your software project. **The build process includes generating source code, compiling the source, generating XML files (e.g. deployment descriptors), packaging the compiled code and files into a JAR file etc.** Building the software is typically done by an automatic build tool like Apache Ant or Apache Maven. Build tools may scan your Java code for specific annotations and generate source code or other files based on these annotations.

Normally, Java annotations are not present in your Java code after compilation. It is possible, however, to define your own annotations that are available at runtime. These annotations can then be accessed via [Java Reflection](http://tutorials.jenkov.com/java-reflection/index.html), and used to give instructions to your program, or some third party API.



## Annotation Basics

```java
@Entity
```



### Annotation Elements

```java
@Entity(tableName = "vehicles")
```

An annotation can contain multiple elements. 

```java
@Entity(tableName = "vehicles", primaryKey = "id")
```

When an annotation just contains a single element named `value`, you can leave out the element name, and just provide the value.

```java
@InsertNew(value = "yes")
```

```java
@InsertNew("yes")
```



## Annotation Placement

You can place Java annotations above **classes, interfaces, methods, method parameters, fields and local variables**.

```java
@Entity
public class Vehicle {

    @Persistent
    protected String vehicleName = null;


    @Getter
    public String getVehicleName() {
        return this.vehicleName;
    }

    public void setVehicleName(@Optional vehicleName) {
        this.vehicleName = vehicleName;
    }

    public List addVehicleNameToList(List names) {

        @Optional
        List localNames = names;

        if(localNames == null) {
            localNames = new ArrayList();
        }
        localNames.add(getVehicleName());

        return localNames;
    }

}
```

## 

## Built-in Java Annotations

Java comes with three built-in annotations which are used to give the Java compiler instructions. These annotations are:

- @Deprecated
- @Override
- @SuppressWarnings



### @Deprecated

The `@Deprecated` annotation is used to mark a class, method or field as deprecated, meaning it should no longer be used.    If your code uses deprecated classes, methods or fields, the compiler will give you a warning.    Here is `@Deprecated` Java annotation example:

```java
@Deprecated
public class MyComponent {

}
```

The use of the `@Deprecated` Java annotation above the class declaration marks the class as deprecated.

You can also use the `@Deprecated` annotation above method and field declarations, to mark the method    or field as deprecated.

When you use the `@Deprecated` annotation, it is a good idea to also use the corresponding `@deprecated`    JavaDoc symbol, and explain why the class, method or field is deprecated, and what the programmer should use instead.    For instance:

```java
@Deprecated
/**
  @deprecated Use MyNewComponent instead.
*/
public class MyComponent {

}
```



### @Override

The `@Override` Java annotation is used above methods that override methods in a superclass. If the method does not match a method in the superclass, the compiler will give you an error.

The `@Override` annotation is not necessary in order to override a method in a superclass. It is a good idea to use it still, though. In case someone changed the name of the overridden method in the superclass, your subclass method would no longer override it. Without the `@Override` annotation you would not find out.

```java
public class MySuperClass {

    public void doTheThing() {
        System.out.println("Do the thing");
    }
}


public class MySubClass extends MySuperClass{

    @Override
    public void doTheThing() {
        System.out.println("Do it differently");
    }
}
```

In case the method `doTheThing()` in `MySuperClass` changes signature so that the same method    in the subclass no longer overrides it, the compiler will generate an error.



### @SuppressWarnings

The `@SuppressWarnings` annotation makes the compiler suppress warnings for a given method. For instance, if a method calls a deprecated method, or makes an insecure type cast, the compiler    may generate a warning. You can suppress these warnings by annotating the method containing the code with the `@SuppressWarnings` annotation.

```java
@SuppressWarnings
public void methodWithWarning() {
}
```



## Creating Your Own Annotations

It is possible to create your own (custom) Java annotations. Annotations are defined in their own file, just like a Java class or interface. Here is custom Java annotation example:

```java
@interface MyAnnotation {

    String   value();

    String   name();
    int      age();
    String[] newNames();

}
```

This example defines an annotation called `MyAnnotation` which has four elements. Notice the `@interface` keyword. This signals to the Java compiler that this is a Java annotation definition.

Notice that each element is defined similarly to a method definition in an interface. It has a data type and a name. **You can use all primitive data types as element data types. You can also use arrays as data type. You cannot use complex objects as data type.**

To use the above annotation, you could use code like this:

```java
@MyAnnotation(
    value="123",
    name="Jakob",
    age=37,
    newNames={"Jenkov", "Peterson"}
)
public class MyClass {
}
```



### Element Default Values

You can specify default values for an element. That way the element becomes optional and can be left out.

```java
@interface MyAnnotation {

    String   value() default "";

    String   name();
    int      age();
    String[] newNames();

}
```

The `value` element can now be left out when using the annotation. If you leave it out, it will be considered as if you had used the default value for the `value` element. Here is an example of an annotation with an element value left out, so that element is set to the default value:

```java
@MyAnnotation(
    name="Jakob",
    age=37,
    newNames={"Jenkov", "Peterson"}
)
public class MyClass {


}
```

Notice that the `value` element is no longer present.



### @Retention

You can specify for your custom annotation if it should be available at runtime, for inspection via reflection. 

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)

@interface MyAnnotation {

    String   value() default "";

}
```

Notice the `@Retention` annotation added above the `MyAnnotation` definition:

```java
@Retention(RetentionPolicy.RUNTIME)
```

This is what signals to the Java compiler and JVM that the annotation should be available via reflection at runtime. 



The `RetentionPolicy` class contains two more values you can use:

`RetentionPolicy.CLASS` means that **the annotation is stored in the .class file, but not available at runtime**. This is the **default retention policy**, if you do not specify any retention policy at all.

 `RetentionPolicy.SOURCE` means that **the annotation is only available in the source code**, and not in the .class files and not a runtime. If you create  your own annotations for use with build tools that scan the code, you can use this retention policy. That way the .class files are not polluted unnecessarily.



### @Target

You can specify which Java elements your custom annotation can be used to annotate. You do so by annotating your annotation definition with the `@Target` annotation.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target({ElementType.METHOD})
public @interface MyAnnotation {

    String   value();
}
```

This example shows a Java annotation that can only be used to annotate methods.

The `ElementType` class contains the following possible targets:

- ElementType.ANNOTATION_TYPE
- ElementType.CONSTRUCTOR
- ElementType.FIELD
- ElementType.LOCAL_VARIABLE
- ElementType.METHOD
- ElementType.PACKAGE
- ElementType.PARAMETER
- ElementType.TYPE

Most of these are self explaining, but a few are not. Therefore I will explain the targets which are not obvious.

The `ANNOTATION_TYPE` target means Java annotation definitions. Thus, the annotation can only be used to annotate other annotations. Like the `@Target` and `@Retention` annotations.

The `TYPE` target means any type. A type is either a class, interface, enum or annotation.



### @Inherited

The `@Inherited` annotation signals that a custom Java annotation used in a class should be inherited    by subclasses inheriting from that class. Here is an `@Inherited` Java annotation example:

```java
java.lang.annotation.Inherited

@Inherited
public @interface MyAnnotation {

}
```

```java
@MyAnnotation
public class MySuperClass { ... }

public class MySubClass extends MySuperClass { ... }
```

In this example the class `MySubClass` inherits the annotation `@MyAnnotation`  because `MySubClass` inherits from `MySuperClass`, and `MySuperClass` has a `@MyAnnotation` annotation.



### @Documented

The `@Documented` annotation is used to signal to the JavaDoc tool that your custom annotation should be visible in the JavaDoc for classes using your custom annotation. Here is a `@Documented`  Java annotation example:

```java
import java.lang.annotation.Documented;

@Documented
public @interface MyAnnotation {

}
```

```java
@MyAnnotation
public class MySuperClass { ... }
```

When generating JavaDoc for the `MySuperClass` class, the `@MyAnnotation` is now included    in the JavaDoc.

You will not use the `@Documented` annotation often, but now you know it exists, if you should need it.



### Practice

```java
/**
 * Created by WS on 2017/6/20.
 */

@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface API {
  
    String value() default "API-name";
    boolean auth() default true;  
  
}
```



# References

[Java Annotations](http://tutorials.jenkov.com/java/annotations.html)



---

created at 2017-06-20 12:27