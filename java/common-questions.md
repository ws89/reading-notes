# [Do Java primitives go on the Stack or the Heap?](https://stackoverflow.com/questions/3646632/do-java-primitives-go-on-the-stack-or-the-heap)

Primitives defined locally would be on the stack.  However if a primitive were defined as part of an instance of an object, that primitive would be on the heap.

```java
public class Test
{
    private static class HeapClass
    {
        public int y; // When an instance of HeapClass is allocated, this will be on the heap.
    }
    public static void main(String[] args)
    {
        int x=1; // This is on the stack.
    }
}
```





---

created at 2017-05-26