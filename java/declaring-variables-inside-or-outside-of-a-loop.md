# Best Coding Practice

**The scope of local variables should always be the smallest possible.**

Declaring objects in the *smallest scope* improve **readability**.

```java
while (condition) {
    String str = calculateStr();
    .....
}
```

At the end of each loop iteration, the variable goes out of scope and ceases to exist. it just means that the memory is available to be used by other things. 




# References

[**declaring-variables-inside-or-outside-of-a-loop**](http://stackoverflow.com/questions/8803674/declaring-variables-inside-or-outside-of-a-loop)

[Declaring Variables Inside or Outside of a Loop](http://www.gunduru.com/post/107249109253/declaring-variables-inside-or-outside-of-a-loop)

[**are local variables made inside a loop destroyed when the loop starts over?**](http://stackoverflow.com/questions/33643468/are-local-variables-made-inside-a-loop-destroyed-when-the-loop-starts-over)



---

created at 2017-04-09 22:00