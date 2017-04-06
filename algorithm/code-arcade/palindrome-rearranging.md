# Description

Given a string, find out if its characters can be rearranged to form a palindrome.
A palindrome is a string that reads the same left-to-right and right-to-left.

**Example**

For `inputString = "aabb"`, the output should be
`palindromeRearranging(inputString) = true`.

We can rearrange `"aabb"` to make `"abba"`, which is a palindrome.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  A string consisting of lowercase English letters.

  *Guaranteed constraints:*
  `4 ≤ inputString.length ≤ 50`.

- **[output] boolean**

  `true` if the characters of the `inputString` can be rearranged to form a palindrome, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean palindromeRearranging(String inputString) {

    int []niz = new int[26];
    
    for(int i=0; i<inputString.length(); i++)
        niz[inputString.charAt(i)-97]++;
    
    int cnt=0;
    for(int i=0; i<niz.length; i++)
        if(niz[i]%2!=0)
            cnt++;
    
    return cnt<=1;
}
```

**Mine**

``` java
boolean palindromeRearranging(String inputString) {
    
    Map<Character,Integer> map = new HashMap();
    for( int i=0; i<inputString.length(); i++ ){
        Character key = inputString.charAt(i);
        Integer value = map.get(key) == null ? 1 : map.get(key)+1;
        map.put(key,value);
    }
    
    boolean odd = false;
    for(Integer count : map.values()){
        if( count%2 != 0 ){
            if(odd){// only odd once
                return false;
            }
            odd = true;
        }
    }
    return true;
}
```

# Source

https://codefights.com/arcade/intro/level-4/Xfeo7r9SBSpo3Wico



---

created at 2017-04-06 17:30 