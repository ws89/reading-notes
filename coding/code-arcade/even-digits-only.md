# Description

Check if all digits of the given integer are even.

**Example**

- For `n = 248622`, the output should be
  `evenDigitsOnly(n) = true`;
- For `n = 642386`, the output should be
  `evenDigitsOnly(n) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  *Guaranteed constraints:*
  `1 ≤ n ≤ 109`.

- **[output] boolean**

  `true` if all digits of `n` are even, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean evenDigitsOnly(int n) {
    if (n == 0) {
      return true;
    }
    if (n % 2 != 0) {
      return false;
    }
    return evenDigitsOnly(n / 10);
}
```

**Mine**

``` java
boolean evenDigitsOnly(int n) {
    String s = n + "";
    char c0 = '0';
    boolean allEven = true;
    for(int i=0; i<s.length(); i++){
        if( (s.charAt(i) - c0) % 2 != 0 ){
            allEven = false;
            break;
        }
    }
    return allEven;
}
```

# Source

https://codefights.com/arcade/intro/level-6/6cmcmszJQr6GQzRwW

---

created at 2017-04-08 23:40 