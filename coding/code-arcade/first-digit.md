# Description

Find the leftmost digit that occurs in a given string.

**Example**

- For `inputString = "var_1__Int"`, the output should be
  `firstDigit(inputString) = '1'`;
- For `inputString = "q2q-q"`, the output should be
  `firstDigit(inputString) = '2'`;
- For `inputString = "0ss"`, the output should be
  `firstDigit(inputString) = '0'`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  A string containing at least one digit.

  *Guaranteed constraints:*
  `3 ≤ inputString.length ≤ 10`.

- **[output] char**

# Solutions

**Best Votes**

``` java
char firstDigit(String inputString) {
    return inputString.replaceAll("[^0-9]","").charAt(0);
}
```

``` java
char firstDigit(String inputString) {
    inputString = inputString.replaceFirst("(\\D*)(\\d)(.*)", "$2");
    return inputString.charAt(0);
}
```

**Mine**

``` java
char firstDigit(String inputString) {
    for(int i=0; i<inputString.length(); i++){
        char c = inputString.charAt(i);
        if( c>='0' && c<='9' ){
            return c;
        }
    }
    return '0';
}
```

# Source

https://codefights.com/arcade/intro/level-8/rRGGbTtwZe2mA8Wov

---

created at 2017-04-13 11:52