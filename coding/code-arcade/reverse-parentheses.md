# Description

You have a string `s` that consists of English letters, punctuation marks, whitespace characters, and brackets. It is guaranteed that the parentheses in `s` form a [regular bracket sequence](keyword://regular-bracket-sequence).

Your task is to reverse the strings contained in each pair of matching parentheses, starting from the innermost pair. The results string should not contain any parentheses.

**Example**

For string  `s = "a(bc)de"`,  the output should be
`reverseParentheses(s) = "acbde"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string s**

  A string consisting of English letters, punctuation marks, whitespace characters and brackets. It is guaranteed that parentheses form a *regular bracket sequence*.

  *Constraints:*
  `5 ≤ s.length ≤ 55`.

- **[output] string**

# Solutions

**Best Votes**

``` java
String reverseParentheses(String s) {
    int st = s.lastIndexOf('(');
    while(st != -1) {
        int ed = s.indexOf(')', st);
        String t = s.substring(0, st);
        t += new StringBuffer(s.substring(st + 1, ed)).reverse().toString();
        t += s.substring(ed + 1);
        s = t;
        st = s.lastIndexOf('(');
    }
    return s;
}
```

**Mine**

``` java
String reverseParentheses(String s) {
    // a(bcdefghijkl(mno)p)q  -> apmnolkjihgfedcbq
    if ( s.lastIndexOf("(") == -1 ){
        return s;
    }

    StringBuffer sb = new StringBuffer(s);
    Set<Integer> handledIndexes = new LinkedHashSet();
    Set<Integer> parenthesesIndex = getLastIndexes(s,"(");
    List<Map<Integer,Integer>> list = new ArrayList();
    for(Integer index : parenthesesIndex){
        //search the "(" corresponding ")"'s index
        int left = index;
        int right = index+1;
        while (true){
            if( sb.charAt(right) == ')' && !handledIndexes.contains(right) ){
                break;
            }
            right++;
        }
        //record reverse corresponding index pair
        //(ab) (a)
        while (left <= right){
            Map<Integer,Integer> swapIndexPair = new HashMap();
            swapIndexPair.put(left,right);
            list.add(swapIndexPair);
            handledIndexes.add(left);
            handledIndexes.add(right);
            left++;
            right--;
        }
    }

    //swap
    for(Map<Integer,Integer> map : list){
        Map.Entry<Integer,Integer> entry = map.entrySet().iterator().next();
        int leftIndex = entry.getKey();
        int rightIndex = entry.getValue();
        char left = sb.charAt( leftIndex );
        char right = sb.charAt( rightIndex );

        char temp = left;
        sb.setCharAt(leftIndex,right);
        sb.setCharAt(rightIndex,temp);
    }

    //delete ( and )
    s = sb.toString().replace("(","");
    s = s.replace(")","");
    return s;
}

/**
* get '(' indexes by inverted order
*/
private Set<Integer> getLastIndexes(String s,String p){
    // omit basic check
    StringBuilder sb = new StringBuilder(s);
    Set<Integer> lastIndexes = new LinkedHashSet();
    int lastIndex = sb.lastIndexOf(p);
    char replaceChar =  (char)(p.charAt(0)+1) ;
    while( lastIndex != -1 ){
        lastIndexes.add( lastIndex );
        sb.setCharAt(lastIndex, replaceChar);
        lastIndex = sb.lastIndexOf(p);
    }
    return lastIndexes;
}
```

# Source

https://codefights.com/arcade/intro/level-3/3o6QFqgYSontKsyk4



---

created at 2017-04-05 17:53 