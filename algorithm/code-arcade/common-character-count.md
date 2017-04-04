# Description

Given two strings, find the number of common characters between them.

**Example**

For `s1 = "aabcc"` and `s2 = "adcaa"`, the output should be
`commonCharacterCount(s1, s2) = 3`.

Strings have `3` common characters - `2` "a"s and `1` "c".

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string s1**

  A string consisting of lowercase latin letters `a-z`.

  *Guaranteed constraints:*
  `3 ≤ s1.length ≤ 15`.

- **[input] string s2**

  A string consisting of lowercase latin letters `a-z`.

  *Guaranteed constraints:*
  `4 ≤ s2.length ≤ 15`.

- **[output] integer**



# Solutions

**Best Votes**

``` java
int commonCharacterCount(String s1, String s2) {
    int[] a = new int[26], 
        b = new int[26];
    for (char c: s1.toCharArray())
        a[c - 'a']++;
    for (char c: s2.toCharArray())
        b[c - 'a']++;
    int s = 0;
    for (int i = 0; i < 26; ++i) 
        s +=Math.min(a[i],b[i]);
    return s;
        
}
```

**Mine**

``` java
int commonCharacterCount(String s1,String s2){
    char[] chars = s2.toCharArray();
    List<Character> characterList = new ArrayList();
    for(char c : chars){
        characterList.add(c);
    }

    int sum = 0;
    for(int i=0; i<s1.length(); i++){
        int index = characterList.indexOf( s1.charAt(i) );
        if(index != -1){
            characterList.set(index,'.');
            sum++;
        }
    }
    return sum;
}
```

# Source

https://codefights.com/arcade/intro/level-3/JKKuHJknZNj4YGL32



---

created at 2017-04-04 16:40