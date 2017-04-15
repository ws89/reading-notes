# Description

Given a string, find the shortest possible string which can be achieved by adding characters to the end of initial string to make it a [palindrome](keyword://palindrome).

**Example**

For `st = "abcdc"`, the output should be
`buildPalindrome(st) = "abcdcba"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string st**

  A string consisting of lowercase latin letters.

  *Guaranteed constraints:*
  `3 ≤ st.length ≤ 10`.

- **[output] string**



# Solutions

**Best Votes**

``` java

```

**Mine**

``` java
String buildPalindrome(String st) {    
    StringBuilder reverseSb = new StringBuilder(st);
    reverseSb.reverse();
    
    String start = reverseSb.substring(0,1);
    int fromIndex = 0;
    int index = st.indexOf(start,fromIndex);
    String subString = "";
    while( index != -1 ){
        subString = st.substring(index);
        if( reverseSb.indexOf(subString) == 0 ){
            break;
        }
        fromIndex = index+1;
        index = st.indexOf(start,fromIndex);
    }    
    return st + reverseSb.substring(subString.length());
}
// abcdc - abcdcba
// abcabc
// 
// 
// abcdc
//   cdcba
//   
// abcdac  
//      cadcba
// abcbe
//     ebcba
```

# Source

https://codefights.com/arcade/intro/level-10/ppZ9zSufpjyzAsSEx

---

created at 2017-04-15 16:20