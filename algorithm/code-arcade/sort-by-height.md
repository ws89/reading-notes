# Description

Some people are standing in a row in a park. There are trees between them which cannot be moved. Your task is to rearrange the people by their heights in a non-descending order without moving the trees.

**Example**

For `a = [-1, 150, 190, 170, -1, -1, 160, 180]`, the output should be
`sortByHeight(a) = [-1, 150, 160, 170, -1, -1, 180, 190]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer a**

  If `a[i] = -1`, then the `ith` position is occupied by a tree. Otherwise `a[i]` is the height of a person standing in the `ith` position.

  *Guaranteed constraints:*
  `5 ≤ a.length ≤ 15`,
  `-1 ≤ a[i] ≤ 200`.

- **[output] array.integer**

  Sorted array `a` with all the trees untouched.

# Solutions

**Best Votes**

``` java
int[] sortByHeight(int[] a) {
    ArrayList<Integer> arr = new ArrayList<>();
    for(int i = 0; i < a.length; i++) {
        if (a[i] != -1) arr.add(a[i]);
    }
    Collections.sort(arr);
    int[] b = new int[a.length];
    for(int i = 0; i < a.length; i++) {
        if (a[i] == -1) {
            b[i] = -1;
        } else {
            b[i] = arr.get(0);
            arr.remove(0);
        }
    }    
    return b;
}
```

**Mine**

``` java
int[] sortByHeight(int[] a){
    int temp = 0;
    for(int i=0; i<a.length-1; i++){
        if(a[i] == -1){
            continue;
        }
        // -1,1,-1,3,2,-1  :6
        for(int j=i+1; j<a.length; j++){
            if(a[j] == -1 || a[j]>a[i]){
                continue;
            }
            temp = a[i];
            a[i] = a[j];
            a[j] = temp;
        }
    }
    return a;
}
```

# Source

https://codefights.com/arcade/intro/level-3/D6qmdBL2NYz49XHwM

---

created at 2017-04-04 22:35 