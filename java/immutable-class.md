# Immutable Objects

An object is considered *immutable* if its state cannot change after it is constructed.

Immutable objects are particularly useful in concurrent applications. 
Since they cannot change state, they cannot be corrupted by thread interference or observed in an inconsistent state.

*(由于它们不能改变状态，它们不会被线程干扰损坏或者在不一致状态下观察到。)*



# Strategies

1. Don't provide "setter" methods — methods that modify fields or objects referred to by fields.
2. Make all fields `final` and `private`.
3. Don't allow subclasses to override methods. The simplest way to do this is to declare the class as `final`. A more sophisticated approach is to make the constructor `private` and construct instances in factory methods.
4. If the instance fields include references to mutable objects, don't allow those objects to be changed:
   - Don't provide methods that modify the mutable objects.
   - Don't share references to the mutable objects. Never store references to external, mutable objects passed to the constructor; if necessary, create copies, and store references to the copies. Similarly, create copies of your internal mutable objects when necessary to avoid returning the originals in your methods.



# References

[Immutable Objects](https://docs.oracle.com/javase/tutorial/essential/concurrency/immutable.html)

[A Synchronized Class Example](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncrgb.html)

[A Strategy for Defining Immutable Objects](https://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html)



---

created at 2017-04-27 01:45