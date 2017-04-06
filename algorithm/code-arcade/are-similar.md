# Description

Two arrays are called *similar* if one can be obtained from another by swapping at most one pair of elements in one of the arrays.

Given two arrays, check whether they are *similar*.

**Example**

- For `A = [1, 2, 3]` and `B = [1, 2, 3]`, the output should be
  `areSimilar(A, B) = true`.

  The arrays are equal, no need to swap any elements.

- For `A = [1, 2, 3]` and `B = [2, 1, 3]`, the output should be
  `areSimilar(A, B) = true`.

  We can obtain `B` from `A` by swapping `2` and `1` in `B`.

- For `A = [1, 2, 2]` and `B = [2, 1, 1]`, the output should be
  `areSimilar(A, B) = false`.

  Any swap of any two elements either in `A` or in `B` won't make `A` and `B` equal.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer A**

  Array of integers.

  *Guaranteed constraints:*
  `3 ≤ A.length ≤ 105`,
  `1 ≤ A[i] ≤ 1000`.

- **[input] array.integer B**

  Array of integers of the same length as `A`.

  *Guaranteed constraints:*
  `B.length = A.length`,
  `1 ≤ B[i] ≤ 1000`.

- **[output] boolean**

  `true` if `A` and `B` are similar, `false` otherwise.

# Solutions

**Best Votes**

``` java

```

**Mine**

optimize mine V1

``` java
boolean areSimilar(int[] A, int[] B) {    
    Integer aValue = null;
    Integer bValue = null;
    int unequalCount = 0;
    for(int i=0; i<A.length; i++){
        if(A[i] != B[i]){
            unequalCount++;
            
            if(unequalCount == 1){
                aValue = A[i];
                bValue = B[i];
                continue;
            }
            if(unequalCount > 2 ){
                return false;
            }
            // unequalCount is 2
            if( aValue!=B[i] || bValue!=A[i] ){
                return false;
            }
        }        
    }
    return unequalCount==0 || unequalCount==2;
}
```

V1

``` java
boolean areSimilar(int[] A, int[] B) {    
    Integer aValue = null;
    Integer bValue = null;
    int unequalCount = 0;
    for(int i=0; i<A.length; i++){
        if(A[i] == B[i]){
            continue;
        }
        if(A[i] != B[i]){
            unequalCount++;
            if(unequalCount == 1){
                aValue = A[i];
                bValue = B[i];
                continue;
            }
        }
        if(unequalCount > 2 ){
            return false;
        }
        if( aValue!=B[i] || bValue!=A[i] ){
            return false;
        }
    }
    return unequalCount==0 || unequalCount==2;
}
```

# Source

https://codefights.com/arcade/intro/level-4/xYXfzQmnhBvEKJwXP



---

created at 2017-04- 06 16:25