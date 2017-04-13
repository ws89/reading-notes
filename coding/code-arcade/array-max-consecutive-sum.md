# Description

Given array of integers, find the maximal possible sum of some of its `k` consecutive elements.

**Example**

For `inputArray = [2, 3, 5, 1, 6]` and `k = 2`, the output should be
`arrayMaxConsecutiveSum(inputArray, k) = 8`.
All possible sums of `2` consecutive elements are:

- `2 + 3 = 5`;
- `3 + 5 = 8`;
- `5 + 1 = 6`;
- `1 + 6 = 7`.
  Thus, the answer is `8`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  Array of positive integers.

  *Guaranteed constraints:*
  `3 ≤ inputArray.length ≤ 105`,
  `1 ≤ inputArray[i] ≤ 1000`.

- **[input] integer k**

  An integer (not greater than the length of `inputArray`).

  *Guaranteed constraints:*
  `1 ≤ k ≤ inputArray.length`.

- **[output] integer**

  The maximal possible sum.

  ​

# Solutions

**Best Votes**

``` java
int arrayMaxConsecutiveSum(int[] inputArray, int k) {
    int s = 0, ma;
    for(int i = 0; i < k; i++) s += inputArray[i];
    ma = s;
    for(int i = k; i < inputArray.length; i++) {
        s += inputArray[i] - inputArray[i - k];
        ma = Math.max(ma, s);
    }
    return ma;
}
```

**Mine**

``` java
int arrayMaxConsecutiveSum(int[] inputArray, int k) {
    int maxSum = -1;
    
    for(int i=0; i<inputArray.length-k+1; i++){
        int sum = inputArray[i];
        int j = 1;
        while(j<k){
            sum += inputArray[i+j];
            j++;
        }
        maxSum = maxSum>sum ? maxSum : sum;
    }
    return maxSum;
}
```

# Source

https://codefights.com/arcade/intro/level-8/Rqvw3daffNE7sT7d5

---

created at 2017-04-13 14:45 