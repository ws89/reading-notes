# Description

Define a *word* as a sequence of consecutive English letters. Find the longest *word* from the given string.

**Example**

For `text = "Ready, steady, go!"`, the output should be
`longestWord(text) = "steady"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string text**

  *Guaranteed constraints:*
  `4 ≤ text.length ≤ 50`.

- **[output] string**

  The longest *word* from `text`. It's guaranteed that there is a unique output.



# Solutions

**Best Votes**

``` java
String longestWord(String text) {
    String[] words = text.split("[^a-zA-Z]+");
    int iMax = 0;
    int maxLen = 0;
    for (int x = 0; x < words.length; x++)
    {
        if (words[x].length() > maxLen)
        {
            iMax = x;
            maxLen = words[x].length();
        }
    }
    return words[iMax];
}
```




``` java
String longestWord(String text) {
    String[] t = text.split("\\W+");
    String longest = t[0];
    for (String w : t)
        if (w.length() > longest.length())
            longest = w;
    return longest;
}
```

**Mine**

``` java
String longestWord(String text) {
    char[] array = text.toCharArray();
    StringBuilder sb = new StringBuilder();
    StringBuilder sbTemp = new StringBuilder();
    int i = 0;

    while( i<array.length ){
        char c = array[i];
        i++;

        if( Character.isLetter(c) ){
            sbTemp.append(c);
            System.out.println(c);
            if( i != array.length ){
                continue;
            }
        }        

        
        if(sbTemp.length() > sb.length()){
            sb.setLength(0);
            sb.append(sbTemp);
        }
        sbTemp.setLength(0);        
    }
    return sb.toString();
}
```

# Source

https://codefights.com/arcade/intro/level-12/s9qvXv4yTaWg8g4ma



https://docs.oracle.com/javase/7/docs/api/java/lang/Character.html#isLetter

---

created at 2017-04-20 22:30