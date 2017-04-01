# Description

Given an array of integers, find the pair of adjacent elements that has the largest product and return that product.

**Example**

For `inputArray = [3, 6, -2, -5, 7, 3]`, the output should be
`adjacentElementsProduct(inputArray) = 21`.

`7` and `3` produce the largest product.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  An array of integers containing at least two elements.

  *Guaranteed constraints:*
  `2 ≤ inputArray.length ≤ 10`,
  `-1000 ≤ inputArray[i] ≤ 1000`.

- **[output] integer**

  The largest product of adjacent elements.

# Solutions

**Best Votes**

``` java
int adjacentElementsProduct(int[] inputArray) {
    int product = inputArray[0] * inputArray[1];
    for(int i = 1; i < inputArray.length-1; i++){
        int temp = inputArray[i] * inputArray[i+1];
        if(temp > product){
            product = temp;
        }
    }
    return product;
}
```

``` java
int adjacentElementsProduct(int[] inputArray) {
    int ans=Integer.MIN_VALUE;
    for(int i=0; i<inputArray.length-1; i++){
        ans = inputArray[i]*inputArray[i+1]>ans?inputArray[i]*inputArray[i+1]:ans;
    }
    return ans;
}
```



**Mine**

``` java
int adjacentElementsProduct(int[] inputArray) {
    Integer currentMaximum = null;
    for(int i=0; i<inputArray.length-1; ){
        int current = inputArray[i] * inputArray[++i];
        if( currentMaximum == null || current > currentMaximum ){
            currentMaximum = current;
        }
    }
    return currentMaximum;
}
```

# Source

https://codefights.com/arcade/intro/level-2/xzKiBHjhoinnpdh6m/solutions



---

created at 2017-04-01 20:00