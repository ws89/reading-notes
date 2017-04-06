# Description

You are given an array of integers. On each move you are allowed to increase exactly one of its element by one. Find the minimal number of moves required to obtain a strictly increasing sequence from the input.

**Example**

For `inputArray = [1, 1, 1]`, the output should be
`arrayChange(inputArray) = 3`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  *Guaranteed constraints:*
  `3 ≤ inputArray.length ≤ 105`,
  `-105 ≤ inputArray[i] ≤ 105`.

- **[output] integer**

  The minimal number of moves needed to obtain a strictly increasing sequence from `inputArray`.
  It's guaranteed that for the given test cases the answer always fits signed `32`-bit integer type.

# Solutions

**Best Votes**

``` java
int arrayChange(int[] a) {
    int cnt=0, t;    
    for(int i=0; i<a.length-1; i++)
        if(a[i]>=a[i+1]) {
            t = a[i]-a[i+1]+1;
            a[i+1] += t;
            cnt+=t;
        }
    
    return cnt;
}
```

**Mine**

``` java
int arrayChange(int[] inputArray) {
    int number = 0;
    int subscration = 0;
    int increaseValue = 0;
    for(int i=0; i<inputArray.length-1; i++){
        subscration = inputArray[i+1] - inputArray[i];
        if(subscration > 0){
            continue;
        }
        increaseValue = (1 - subscration);
        number += increaseValue;
        inputArray[i+1] += increaseValue; 
    }
    return number;
}
```

# Source

https://codefights.com/arcade/intro/level-4/xvkRbxYkdHdHNCKjg

---

created at 2017-04-06 17:00 