# Description

Given a string, replace each its character by the next one in the English alphabet (`z` would be replaced by `a`).

**Example**

For `inputString = "crazy"`, the output should be
`alphabeticShift(inputString) = "dsbaz"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  Non-empty string consisting of lowercase English characters.

  *Guaranteed constraints:*
  `1 ≤ inputString.length ≤ 10`.

- **[output] string**

  The result string after replacing all of its characters.

# Solutions

**Best Votes**

``` java
String alphabeticShift(String inputString) {
    char[] ss = inputString.toCharArray();
    for (int i = 0; i < ss.length; ++i) {
        ss[i]++;
        if (ss[i] > 'z')
            ss[i] = 'a';
    }
    return new String(ss);
}
```

**Mine**

``` java
String alphabeticShift(String inputString) {
    StringBuilder sb = new StringBuilder();
    for(int i=0; i<inputString.length(); i++){
        char c =  (char)(inputString.charAt(i) + 1);
        if( c > 'z'){
            c = 'a';
        }
        sb.append(c);
    }
    return sb.toString();
}
```

# Source

https://codefights.com/arcade/intro/level-6/PWLT8GBrv9xXy4Dui

---

created at 2017-04-10 00:12 