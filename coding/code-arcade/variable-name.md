# Description

Correct variable names consist only of Latin letters, digits and underscores and they can't start with a digit.

Check if the given string is a correct variable name.

**Example**

- For `name = "var_1__Int"`, the output should be
  `variableName(name) = true`;
- For `name = "qq-q"`, the output should be
  `variableName(name) = false`;
- For `name = "2w2"`, the output should be
  `variableName(name) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string name**

  *Guaranteed constraints:*
  `1 ≤ name.length ≤ 10`.

- **[output] boolean**

  `true` if `name` is a correct variable name, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean variableName(String name) {
    return name.matches("[A-Za-z_][A-Za-z_\\d]*");
}
```

**Mine**

``` java
boolean variableName(String name) {
    char c = '_';
    for(int i=0; i<name.length(); i++){
        c = name.charAt(i);
        if(c == '_' || (c>='a' && c<='z') || (c>='A' && c<='Z') ){
            continue;
        }
        if(c>='0' && c<='9'){
            if(i!=0){
                continue;
            }
        }
        return false;
    }
    return true;
}
```

# Source

https://codefights.com/arcade/intro/level-6/6Wv4WsrsMJ8Y2Fwno

---

created at 2017-04-09 00:01 