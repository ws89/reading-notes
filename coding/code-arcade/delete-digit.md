# Description

Given some integer, find the maximal number you can obtain by deleting exactly one digit of the given number.

**Example**

- For `n = 152`, the output should be
  `deleteDigit(n) = 52`;
- For `n = 1001`, the output should be
  `deleteDigit(n) = 101`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  *Guaranteed constraints:*
  `10 ≤ n ≤ 106`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int deleteDigit(int n)
{
    int max = 0;
    for (int t = 1; t < n; t *= 10)
        max = Math.max(n / 10 / t * t + n % t, max);
    return max;
}
```

**Mine**

``` java
int deleteDigit(int n) {    
    int maximum = 0;    
    StringBuilder sb = new StringBuilder(n + "");    
    int length = sb.length();
    for(int i=0; i<length; i++){
        sb = new StringBuilder(n + "");
        String s = sb.deleteCharAt(i).toString();
        System.out.println(s);
        int c = Integer.valueOf( s );
        System.out.println(c);
        maximum =  c > maximum ? c : maximum ;
    }
    return maximum;
}
```

# Source

https://codefights.com/arcade/intro/level-11/vpfeqDwGZSzYNm2uX

---

created at 2017-04-19 20:41