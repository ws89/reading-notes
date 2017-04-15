# Description

A media access control address (MAC address) is a unique identifier assigned to network interfaces for communications on the physical network segment.

The standard (IEEE 802) format for printing MAC-48 addresses in human-friendly form is six groups of two hexadecimal digits (`0` to `9` or `A` to `F`), separated by hyphens (e.g. `01-23-45-67-89-AB`).

Your task is to check by given string `inputString` whether it corresponds to MAC-48 address or not.

**Example**

- For `inputString = "00-1B-63-84-45-E6"`, the output should be
  `isMAC48Address(inputString) = true`;
- For `inputString = "Z1-1B-63-84-45-E6"`, the output should be
  `isMAC48Address(inputString) = false`;
- For `inputString = "not a MAC-48 address"`, the output should be
  `isMAC48Address(inputString) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  *Guaranteed constraints:*
  `15 ≤ inputString.length ≤ 20`.

- **[output] boolean**

  `true` if `inputString` corresponds to MAC-48 address naming rules, `false` otherwise.



# Solutions

**Best Votes**

``` java
boolean isMAC48Address(String inputString) {    
    return inputString.matches("^([0-9A-F][0-9A-F]-){5}[0-9A-F][0-9A-F]$");
}
```

**Mine**

``` java
boolean isMAC48Address(String inputString) {
    String[] array = inputString.split("-");
    if( array.length != 6 ){
        return false;
    }
    for(String s : array){
        if(s.length() != 2){
            return false;
        }
        char c0 = s.charAt(0);
        char c1 = s.charAt(1);
        if( !isHexadecimal(s.charAt(0)) || !isHexadecimal(s.charAt(1)) ){
            return false;
        }
    }
    if( !isHexadecimal(inputString.charAt(0)) 
           || !isHexadecimal(inputString.charAt(inputString.length()-1)) ){
        return false;
    }
    return true;
}

boolean isHexadecimal(char c){
    if( (c>='0' && c<='9') || (c>='A' && c<='F') ){
        return true;
    }
    return false;
}
```

# Source

https://codefights.com/arcade/intro/level-10/HJ2thsvjL25iCvvdm

---

created at 2017-04-15 18:00