# Description

Given an array of strings, return another array containing all of its longest strings.

**Example**

For `inputArray = ["aba", "aa", "ad", "vcd", "aba"]`, the output should be
`allLongestStrings(inputArray) = ["aba", "vcd", "aba"]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.string inputArray**

  A non-empty array.

  *Guaranteed constraints:*
  `1 ≤ inputArray.length ≤ 10`,
  `1 ≤ inputArray[i].length ≤ 10`.

- **[output] array.string**

  Array of the longest strings, stored in the same order as in the `inputArray`.

# Solutions

**Best Votes**

``` java
String[] allLongestStrings(String[] inputArray) {
    int longest = 0;
    for (int i = 0; i < inputArray.length; i++) {
        if (inputArray[i].length() > longest) {
            longest = inputArray[i].length();
        }
    }
    
    final int longestLength = longest;
    return Stream.of(inputArray)
        .filter(s -> s.length() == longestLength)
        .toArray(String[]::new);
}
```

**Mine**

``` java
String [] allLongestStrings(String[] inputArray){
        int longestStringLength = 0;
        List<String> stringList = new ArrayList();
        for(String s : inputArray){
            if(s.length() > longestStringLength){
                longestStringLength = s.length();
                stringList.clear();
            }
            if(s.length() == longestStringLength){
                stringList.add(s);
            }
        }

        return Arrays.copyOf(stringList.toArray(),stringList.size(),String[].class);
    }
```

# Source

https://codefights.com/arcade/intro/level-3/fzsCQGYbxaEcTr2bL



---

created at 2017-04-04 16:00