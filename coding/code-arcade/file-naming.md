# Description

You are given an array of desired filenames in the order of their creation. Since two files cannot have equal names, the one which comes later will have an addition to its name in a form of `(k)`, where `k` is the smallest positive integer such that the obtained name is not used yet.

Return an array of names that will be given to the files.

**Example**

For `names = ["doc", "doc", "image", "doc(1)", "doc"]`, the output should be
`fileNaming(names) = ["doc", "doc(1)", "image", "doc(1)(1)", "doc(2)"]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.string names**

  *Guaranteed constraints:*
  `5 ≤ names.length ≤ 15`,
  `1 ≤ names[i].length ≤ 15`.

- **[output] array.string**



# Solutions

**Best Votes**


```java
String[] fileNaming(String[] names) {
    HashSet<String> seen = new HashSet<>();
    
    for (int a = 0; a < names.length; a++) {
        String s = names[a];
        if (seen.add(s))
            continue;
        
        for (int n = 1; n < 100; n++) {
            String t = s + "(" + n + ")";
            if (seen.add(t)) {
                names[a] = t;
                break;
            }
        }
    }
    
    return names;
}
```

``` java
Object fileNaming(String[] names) {
    List<String> input = Arrays.asList(names);
    List<String> result = new ArrayList<>();
    for( String s : names ){
        if(result.contains(s)){
            int i = 1;
            for(; result.contains(s+"("+i+")") ;i++){

            }
            s+="("+i+")";
        }
        result.add(s);
    }
    return result;
}
```



**Mine**

``` java
String[] fileNaming(String[] names) {
    Map<String,Integer> map = new HashMap();
    int c = 0;
    for(String temp : names){
        Integer i = map.get(temp);
        i = i==null ? 0 : i;
        
        map.put(temp,++i);
        
        if(i > 1){
           String s = temp + "(" + (i-1) + ")";
           while( map.get(s) != null ){
              i++;
              map.put(temp,i);
              s = temp + "(" + (i-1) + ")";
           }
           names[c] = s;
           map.put(s,1);
        }
        
        c++;
    }
    return names;
}
```

# Source

https://codefights.com/arcade/intro/level-12/sqZ9qDTFHXBNrQeLC



https://docs.oracle.com/javase/7/docs/api/java/util/HashSet.html#add(E)

---

created at 2017-04-27 21:00