# Description

Given the string, check if it is a [palindrome](keyword://palindrome).

**Example**

- For `inputString = "aabaa"`, the output should be
  `checkPalindrome(inputString) = true`;
- For `inputString = "abac"`, the output should be
  `checkPalindrome(inputString) = false`;
- For `inputString = "a"`, the output should be
  `checkPalindrome(inputString) = true`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  A non-empty string consisting of lowercase characters.

  *Guaranteed constraints:*
  `1 ≤ inputString.length ≤ 10`.

- **[output] boolean**

  `true` if `inputString` is a palindrome, `false` otherwise.

# Solutions

**Best Votes**

```java
boolean checkPalindrome(String inputString) {
    return inputString.equals(new StringBuilder(inputString).reverse().toString());
}
```

``` java
boolean checkPalindrome(String inputString) {
    for(int i = 0; i < inputString.length()/2; i++){
        if(inputString.charAt(i) != inputString.charAt(inputString.length()-i-1))
            return false;
    }
    return true;
}
```



**Mine**

V1

```java
boolean checkPalindrome(String s) {
    for(int i = 0;i<s.length(); ){
        for(int j=s.length()-1;j>=0; ){
            if(i>=j){
                return true;
            }
            if(s.charAt(i) != s.charAt(j)){
                return false;
            }

            i++;
            j--;
        }
    }
    return true;
}
```

V2

``` java
boolean checkPalindrome(String s) {
    int i = 0;
    int reverseIndex ;
    while( true ){
        reverseIndex = s.length()-1 - i;
        if( i >=  reverseIndex){
            return true;
        }
        if( s.charAt(i) != s.charAt(reverseIndex)  ){
            return false;
        }
        i++;
    }
}
```



# Source

https://codefights.com/arcade/intro/level-1/s5PbmwxfECC52PWyQ

------

created at 2017-04-01 18:10