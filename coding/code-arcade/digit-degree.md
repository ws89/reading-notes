# Description

Let's define *digit degree* of some positive integer as the number of times we need to replace this number with the sum of its digits until we get to a one digit number.

Given an integer, find its digit degree.

**Example**

- For `n = 5`, the output should be
  `digitDegree(n) = 0`;
- For `n = 100`, the output should be
  `digitDegree(n) = 1`.
  `1 + 0 + 0 = 1`.
- For `n = 91`, the output should be
  `digitDegree(n) = 2`.
  `9 + 1 = 10` -> `1 + 0 = 1`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  *Guaranteed constraints:*
  `5 ≤ n ≤ 109`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int digitDegree(int n)
{
    int r = 0;
    for (; n > 9; r++)
    {
        int t = 0;
        for (; n > 0; n /= 10)
            t += n % 10;
        n = t;
    }
    
    return r;
}
```

**Mine**

``` java
int digitDegree(int n) {
    int count = 0;
    while(true){
        if(n < 10){
            break;
        }
        int temp = 0;
        while(n != 0){
            temp += n%10;
            n = n/10;
        }
        n = temp;
        count++;
    }
    return count;
}
```

# Source

https://codefights.com/arcade/intro/level-9/hoLtYWbjdrD2PF6yo

---

created at 2017-04-14 20:55