# Description

Given array of integers, remove each `kth` element from it.

**Example**

For `inputArray = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]` and `k = 3`, the output should be
`extractEachKth(inputArray, k) = [1, 2, 4, 5, 7, 8, 10]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  *Guaranteed constraints:*
  `5 ≤ inputArray.length ≤ 15`,
  `-20 ≤ inputArray[i] ≤ 20`.

- **[input] integer k**

  *Guaranteed constraints:*
  `1 ≤ k ≤ 10`.

- **[output] array.integer**

  `inputArray` without elements `k - 1`, `2k - 1`, `3k - 1` etc.

# Solutions

**Best Votes**

``` java
int[] extractEachKth(int[] a, int k) {
    int n = a.length, i = 0, m = n - n/k;
    
    int [] res = new int[m];    
  
    for (int j = 0; j <n; j ++) {
        if ((j+1) % k != 0) res[i++] =  a[j];
    }    
    return res;
}
```

**Mine**

``` java
int[] extractEachKth(int[] inputArray, int k) {
    int[] extractArray = new int[ inputArray.length - inputArray.length/k];
    
    for(int newi=0,i=0; i<inputArray.length; i++){
        if( (i+1)%k != 0 ){
            extractArray[newi] = inputArray[i];
            newi++;
        }        
    }
    return extractArray;
}
```

# Source

https://codefights.com/arcade/intro/level-8/3AgqcKrxbwFhd3Z3R

---

created at 2017-04-11 20:40 