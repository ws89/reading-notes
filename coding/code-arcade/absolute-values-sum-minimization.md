# Description

Given a sorted array of integers `a`, find such an integer `x` that the value of

```
abs(a[0] - x) + abs(a[1] - x) + ... + abs(a[a.length - 1] - x)

```

is the *smallest possible* (here `abs` denotes the absolute value).
If there are several possible answers, output the *smallest* one.

**Example**

For `a = [2, 4, 7]`, the output should be
`absoluteValuesSumMinimization(a) = 4`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer a**

  A non-empty array of integers, sorted in ascending order.

  *Guaranteed constraints:*
  `1 ≤ a.length ≤ 200`,
  `-106 ≤ a[i] ≤ 106`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int absoluteValuesSumMinimization(int[] A) {
    return A[(A.length-1)/2];
}
```

**Mine**

``` java
int absoluteValuesSumMinimization(int[] a) {
    if(a.length % 2 == 0){
       return a[(a.length-1)/2];
    }
    return a[a.length/2];
}
```

# Source

https://codefights.com/arcade/intro/level-7/ZFnQkq9RmMiyE6qtq

---

created at 2017-04-10 14:20 