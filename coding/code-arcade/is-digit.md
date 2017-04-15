# Description

Determine if the given character is a digit or not.

**Example**

- For `symbol = '9'`, the output should be
  `isDigit(symbol) = true`;
- For `symbol = '-'`, the output should be
  `isDigit(symbol) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] char symbol**

  A character which is either a digit or not.

- **[output] boolean**

  `true` if `symbol` is a digit, `false` otherwise.



# Solutions

**Best Votes**

``` java
boolean isDigit(char symbol) {
    return Character.isDigit(symbol);
}
```

**Mine**

``` java
boolean isDigit(char symbol) {
    return symbol>='0' && symbol<='9';
}
```

# Source

https://codefights.com/arcade/intro/level-11/Zr2XXEpkBPtYWqPJu

---

created at 2017-04-15 18:20