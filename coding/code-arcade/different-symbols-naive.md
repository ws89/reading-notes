# Description

Given a string, find the number of different characters in it.

**Example**

For `s = "cabca"`, the output should be
`differentSymbolsNaive(s) = 3`.

There are `3` different characters `a`, `b` and `c`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string s**

  A string of lowercase latin letters.

  *Guaranteed constraints:*
  `3 ≤ s.length ≤ 15`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int differentSymbolsNaive(String s)
{
    return (int) s.chars().distinct().count();
}
```

**Mine**

``` java
int differentSymbolsNaive(String s) {
    Set<Character> set = new HashSet();
    for(int i=0; i<s.length(); i++){
        set.add( s.charAt(i) );
    }
    return set.size();
}
```

# Source

https://codefights.com/arcade/intro/level-8/8N7p3MqzGQg5vFJfZ

---

created at 2017-04-13 13:46 