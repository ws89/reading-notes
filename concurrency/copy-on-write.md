# COW

copy-on-write

# Basics

- If a resource is duplicated but not modified, it is not necessary to create a new resource; the resource can be shared between the copy and the original. Modifications must still create a copy, hence the technique: the copy operation is deferred to the first write. By sharing resources in this way, it is possible to significantly reduce the resource consumption of unmodified copies, while adding a small overhead to resource-modifying operations.
- CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

# Characteristics

- lock-free reading
- lock writing
- read-write splitting
- eventual consistency

# Java

- CopyOnWriteArrayList
- CopyOnWriteArraySet


```java
java.util.concurrent.CopyOnWriteArrayList

public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```



# Using

read more write less


# References

[Copy-on-write]: https://en.wikipedia.org/wiki/Copy-on-write
[Java中的CopyOnWrite容器]: http://coolshell.cn/articles/11175.html#_motz_

------

Created at 2017-03-27 19:00