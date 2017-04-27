# Description

Given a rectangular matrix containing only digits, calculate the number of different `2 × 2` squares in it.

**Example**

For

```
matrix = [[1, 2, 1],
          [2, 2, 2],
          [2, 2, 2],
          [1, 2, 3],
          [2, 2, 1]]

```

the output should be
`differentSquares(matrix) = 6`.

Here are all `6` different `2 × 2` squares:

- 1 2
  2 2
- 2 1
  2 2
- 2 2
  2 2
- 2 2
  1 2
- 2 2
  2 3
- 2 3
  2 1

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.array.integer matrix**

  *Guaranteed constraints:*
  `1 ≤ matrix.length ≤ 100`,
  `1 ≤ matrix[i].length ≤ 100`,
  `0 ≤ matrix[i][j] ≤ 9`.

- **[output] integer**

  The number of different `2 × 2` squares in `matrix`.



# Solutions

**Best Votes**

``` java
int differentSquares(int[][] matrix) {
    HashSet<String> set = new HashSet<>();
    for (int i = 0; i < matrix.length-1; i++) {
        for (int j = 0; j < matrix[i].length-1; j++) {
            set.add(matrix[i][j] + " " + matrix[i][j+1] + " "
                   + matrix[i+1][j] + " " + matrix[i+1][j+1]);
        }
    }
    return set.size();
}
```

**Mine**

``` java
int differentSquares(int[][] matrix) {
    if(matrix.length < 2 || matrix[0].length <2){
        return 0;
    }
    
    Set<Integer> set = new HashSet();
    for(int i=1; i<matrix.length; i++){
        for(int j=1; j<matrix[0].length; j++){
            int temp = matrix[i-1][j-1]*1000 + matrix[i-1][j]*100
                        + matrix[i][j-1]*10 + matrix[i][j];
            set.add(temp);
        }
    }
    return set.size();
}
```

# Source

https://codefights.com/arcade/intro/level-12/fQpfgxiY6aGiGHLtv

---

created at 2017-04-27 18:50