# Description

An IP address is a numerical label assigned to each device (e.g., computer, printer) participating in a computer network that uses the Internet Protocol for communication. There are two versions of the Internet protocol, and thus two versions of addresses. One of them is the *IPv4 address*.

IPv4 addresses are represented in dot-decimal notation, which consists of four decimal numbers, each ranging from `0` to `255`, separated by dots, e.g., `172.16.254.1`.

Given a string, find out if it satisfies the IPv4 address naming rules.

**Example**

- For `inputString = "172.16.254.1"`, the output should be
  `isIPv4Address(inputString) = true`;

- For `inputString = "172.316.254.1"`, the output should be
  `isIPv4Address(inputString) = false`.

  `316` is not in range `[0, 255]`.

- For `inputString = ".254.255.0"`, the output should be
  `isIPv4Address(inputString) = false`.

  There is no first number.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  A string consisting of digits, full stops and lowecase Latin letters.

  *Guaranteed constraints:*
  `5 ≤ inputString.length ≤ 15`.

- **[output] boolean**

  `true` if `inputString` satisfies the IPv4 address naming rules, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean isIPv4Address(String inputString) {
    Pattern p = Pattern.compile("^([01]?\\d\\d?|2[0-4]\\d|25[0-5])\\.([01]?\\d\\d?|2[0-4]\\d|25[0-5])\\.([01]?\\d\\d?|2[0-4]\\d|25[0-5])\\.([01]?\\d\\d?|2[0-4]\\d|25[0-5])$");
    Matcher m = p.matcher(inputString);
    return m.matches();
}
```



``` java
boolean isIPv4Address(String inputString) {
    String[] splitString = inputString.split("[.]");
    if (splitString.length > 4) {
        return false;
    }
    for (String string : splitString) {
        if (string.isEmpty()) {
            return false;
        }
        if (!string.matches("[0-9]{1,3}")) {
            return false;
        }
        int number = Integer.parseInt(string);
        if (!(number >= 0 && number <= 255)) {
            return false;
        }
    }
    return true;
}
```

**Mine**

``` java
boolean isIPv4Address(String inputString) {
    
    String[] strings = inputString.split("\\.");
    if(strings.length != 4){
        return false;
    }
    for(String s : strings){
        try{
            int i = Integer.valueOf(s);
            if(i<0 || i>255){
                return false;
            }
        }catch(Exception e){
            return false;
        } 
    }
    return true;
}

```

# Source

https://codefights.com/arcade/intro/level-5/veW5xJednTy4qcjso

---

created at 2017-04-06 18:32 