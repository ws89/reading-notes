# Description

Given an array of integers, find the maximal absolute difference between any two of its adjacent elements.

**Example**

For `inputArray = [2, 4, 1, 0]`, the output should be
`arrayMaximalAdjacentDifference(inputArray) = 3`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  *Guaranteed constraints:*
  `3 ≤ inputArray.length ≤ 10`,
  `-15 ≤ inputArray[i] ≤ 15`.

- **[output] integer**

  The maximal absolute difference.

# Solutions

**Best Votes**

``` java
int arrayMaximalAdjacentDifference(int[] inputArray) {
    int max=0;
    for( int i = 1 ; i < inputArray.length ; i++ ){
        max = Math.max(max,Math.abs(inputArray[i] - inputArray[i-1]));
    }
    return max;
}
```

**Mine**

``` java
int arrayMaximalAdjacentDifference(int[] inputArray) {
    int maximalAbsoluteDifference = 0;
    
    for(int i=0; i<inputArray.length-1; i++){
        int difference = inputArray[i+1] - inputArray[i];
        difference = difference<0 ? difference*-1 : difference;
        maximalAbsoluteDifference = maximalAbsoluteDifference < difference ? difference :maximalAbsoluteDifference;
    }
    return maximalAbsoluteDifference;
}
```

# Source

https://codefights.com/arcade/intro/level-5/EEJxjQ7oo7C5wAGjE

---

created at 2017-04-06 18:17 