# Description

A string is said to be *beautiful* if `b` occurs in it no more times than `a`; `c` occurs in it no more times than `b`; etc.

Given a string, check whether it is *beautiful*.

**Example**

- For `inputString = "bbbaacdafe"`, the output should be
  `isBeautifulString(inputString) = true`;
- For `inputString = "aabbb"`, the output should be
  `isBeautifulString(inputString) = false`;
- For `inputString = "bbc"`, the output should be
  `isBeautifulString(inputString) = false`.

> It may help to note that inputString = "bbc" is false because there are 0 a's.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  A string of lowercase letters.

  *Guaranteed constraints:*
  `3 ≤ inputString.length ≤ 50`.

- **[output] boolean**



# Solutions

**Best Votes**

``` java

```

**Mine**

``` java
boolean isBeautifulString(String inputString) {
    int[] letterTimes = new int[26];
    for(int i=0; i<inputString.length(); i++){
        letterTimes[inputString.charAt(i) - 'a']++;
    }
    
    for(int i=1; i<letterTimes.length; i++){
        if(letterTimes[i] > letterTimes[i-1]){
            return false;
        }
    }
    return true;
}
```

# Source

https://codefights.com/arcade/intro/level-10/PHSQhLEw3K2CmhhXE

---

created at 2017-04-15 15:10