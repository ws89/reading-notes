# Description

Below we will define an `n`-interesting polygon. Your task is to find the area of a polygon for a given `n`.

A `1`-interesting polygon is just a square with a side of length `1`. An `n`-interesting polygon is obtained by taking the `n - 1`-interesting polygon and appending `1`-interesting polygons to its rim, side by side. You can see the `1`-, `2`-, `3`- and `4`-interesting polygons in the picture below.

![](https://codefightsuserpics.s3.amazonaws.com/tasks/shapeArea/img/area.png)
<https://codefightsuserpics.s3.amazonaws.com/tasks/shapeArea/img/area.png>

**Example**

- For `n = 2`, the output should be
  `shapeArea(n) = 5`;
- For `n = 3`, the output should be
  `shapeArea(n) = 13`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  *Constraints:*
  `1 ≤ n < 104`.

- **[output] integer**

  The area of the `n`-interesting polygon.

# Solutions

**Best Votes**

``` java
int shapeArea(int n) {
  return (n*n)+( (n-1)*(n-1) ); 
}
```

```java
int shapeArea(int n) {
  if ( n == 1 ) return 1;
  return ( shapeArea(n-1) + (n-2)*4 + 4);
}
```

``` java
int shapeArea(int n) {
    return (int) ( Math.pow(n,2) + ( Math.pow(n-1,2) ) );
}
```



**Mine**

``` java
int shapeArea(int n) {
    int sum = 0;
    int i = n;
    while(i>0){
        int current = 1 + (i-1)*2;
        if(i != n){
            current *= 2;
        }
        sum += current;
        
        i--;
    }
    return sum;
}
```

# Source

https://codefights.com/arcade/intro/level-2/yuGuHvcCaFCKk56rJ/solutions



# References

http://www.w3school.com.cn/jsref/jsref_pow.asp

---

created at 2017-04-01 20:40
