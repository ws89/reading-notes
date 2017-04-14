# Description

Given a string, output its longest [prefix](keyword://string-prefix) which contains only digits.

**Example**

For `inputString="123aa1"`, the output should be
`longestDigitsPrefix(inputString) = "123"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  *Guaranteed constraints:*
  `3 ≤ inputString.length ≤ 35`.

- **[output] string**

# Solutions

**Best Votes**

``` java
String longestDigitsPrefix(String inputString) {
    return inputString.replaceAll("^(\\d*).*","$1");
}
```

**Mine**

``` java
String longestDigitsPrefix(String inputString) {
    StringBuilder sb = new StringBuilder();
    
    for(int i=0; i<inputString.length(); i++){
        char c = inputString.charAt(i);
        if( c >= '0' && c <= '9'){
            sb.append(c);
            continue;
        }
        break;
    }
    return sb.toString();
}
```

# Source

https://codefights.com/arcade/intro/level-9/AACpNbZANCkhHWNs3

---

created at 2017-04-14 20:26