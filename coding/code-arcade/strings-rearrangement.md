# Description

Given an array of equal-length strings, check if it is possible to rearrange the strings in such a way that after the rearrangement the strings at consecutive positions would differ by exactly one character.

**Example**

- For `inputArray = ["aba", "bbb", "bab"]`, the output should be
  `stringsRearrangement(inputArray) = false`;
- For `inputArray = ["ab", "bb", "aa"]`, the output should be
  `stringsRearrangement(inputArray) = true`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.string inputArray**

  A non-empty array of strings of lowercase letters.

  *Guaranteed constraints:*
  `2 ≤ inputArray.length ≤ 10`,
  `1 ≤ inputArray[i].length ≤ 15`.

- **[output] boolean**

# Solutions

**Best Votes**

``` java
boolean stringsRearrangement(String[] inputArray) {
   
    boolean[] used = new boolean[inputArray.length];
    findSequence(inputArray, null, used, 0);
    return success;
}

boolean success = false; // modified by findSequence

// recursive backtracking procedure to find admissible
// sequence of strings in the array. String prev is the
// previous string in the sequence, used[] keeps track 
// of which strings have been used so far, and n is the
// current length of the sequence.
void findSequence(String[] a, String prev, boolean[] used, int n) {
    if (n == a.length) {
        success = true;
        return;
    }
    for (int i = 0; i < a.length; i++) {
        if (!used[i] && (prev == null || differByOne(prev, a[i]))) {
            used[i] = true;
            findSequence(a, a[i], used, n+1);
            used[i] = false;
        }
    }
}

boolean differByOne(String a, String b) {
    int count = 0;
    for (int i = 0; i < a.length(); i++) {
        if (a.charAt(i) != b.charAt(i)) {
            count++;
        }
    }
    return count == 1;
}

```

**Mine**

``` java
boolean stringsRearrangement(String[] inputArray) {
    /* find tree relations */
    Map<Integer,List<Integer>> map = toMap(inputArray);
    //print map for debug.
    for(Map.Entry<Integer,List<Integer>> entry : map.entrySet()){
        System.out.println(entry.getKey() + " -- " + entry.getValue());
    }


    /* if some node doesn't has any children */

    /* print tree chains */
    Set<String> pathSet = new LinkedHashSet();
    Set<String> chains = new LinkedHashSet();
    for(int i=0; i<inputArray.length; i++){//TODO
        StringBuilder sb = new StringBuilder(",");
        sb.append(i);
        printTreeChains(map, i, pathSet, sb, chains);
    }
    /* loop chains */
    int count = 0;
    for(String s : chains){
        count++;
        if( s.toString().split(",").length == inputArray.length+1 ){
            System.out.println("succeed: " + count + "\t" + s);
            return true;
        }
    }
    return false;
}

void printTreeChains(Map<Integer,List<Integer>> map,Integer parentNode,Set<String> pathSet,StringBuilder pathKey,Set<String> chains){
    /* getNodes */
    List<Integer> children = map.get(parentNode);
    StringBuilder parent = new StringBuilder(pathKey.toString());
    /* if leafNode stop*/
    if(children==null || children.isEmpty()){
        // ?
        return;
    }

    boolean add = false;
    for(Integer now : children){
        String pk = pathKey.toString() + "," + now;
        if( pathSet.contains(pk) ){
            continue;
        }
        if( Arrays.asList( pathKey.toString().split(",") ).contains(now+"") ){
            continue;
        }

        add = true;
        pathKey.append(",").append(now);
        pathSet.add(pathKey.toString());
        printTreeChains(map,now,pathSet,pathKey,chains);

        if( add ){
            chains.add(pathKey.toString());
           // System.out.println("[---]" + pathKey);
            printTreeChains(map, parentNode, pathSet, parent, chains);
            break;
        }
    }
}

//find tree relation
Map toMap(String [] inputArray){
    Map<Integer,List<Integer>> map = new LinkedHashMap();
    for(int i=0; i<inputArray.length; i++){
        int key = i;
        List<Integer> list = new ArrayList();
        for(int j=0; j<inputArray.length; j++){
            if(i == j){
                continue;
            }

            if( differOne(inputArray[i],inputArray[j]) ){
                list.add(j);
            }
        }
        map.put(key,list);
    }
    return map;
}

//compare if strings differ size is one
boolean differOne(String s1,String s2){
    int size = 0;
    for(int i=0; i<s1.length(); i++ ){
        if(s1.charAt(i) != s2.charAt(i)){
            size++;
        }
        if(size>1){
            return false;
        }
    }
    return size == 1;
}
```

# Source

https://codefights.com/arcade/intro/level-7/PTWhv2oWqd6p4AHB9

---

created at 2017-04-10 18:00 