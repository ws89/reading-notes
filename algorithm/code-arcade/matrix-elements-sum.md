# Description

After becoming famous, CodeBots decided to move to a new building and live together. The building is represented by a rectangular `matrix` of rooms, each cell containing an integer - the price of the room. Some rooms are *free* (their cost is `0`), but that's probably because they are haunted, so all the bots are afraid of them. That is why any room that is *free* or is located anywhere below a *free* room in the same column is not considered suitable for the bots.

Help the bots calculate the total price of all the rooms that are suitable for them.

**Example**

For

```
matrix = [[0, 1, 1, 2], 
          [0, 5, 0, 0], 
          [2, 0, 3, 3]]

```

the output should be
`matrixElementsSum(matrix) = 9`.

Here's the rooms matrix with unsuitable rooms marked with `'x'`:

```
[[x, 1, 1, 2], 
 [x, 5, x, x], 
 [x, x, x, x]]

```

Thus, the answer is `1 + 5 + 1 + 2 = 9`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.array.integer matrix**

  2-dimensional array of integers representing a rectangular matrix of the building.

  *Guaranteed constraints:*
  `1 ≤ matrix.length ≤ 5`,
  `1 ≤ matrix[i].length ≤ 5`,
  `0 ≤ matrix[i][j] ≤ 10`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int matrixElementsSum(int[][] matrix) {
    int s = 0;
    for (int c = 0; c < matrix[0].length; ++c) 
        for (int r = 0; r < matrix.length; ++r) {
            if (matrix[r][c] > 0) 
                s+=matrix[r][c];
            else break;
        }
    return s;
}
```

**Mine**

``` java
int matrixElementsSum(int[][] matrix) {
    int sum = 0;
    Set<Integer> zeroColumns = new HashSet();
    for(int row=0; row<matrix.length; row++){
        for(int column=0; column<matrix[0].length; column++){
            if(zeroColumns.contains(column)){
                continue;
            }

            int current = matrix[row][column];
            if(current == 0){
                zeroColumns.add(column);
                continue;
            }
            sum += current;
        }
    }
    return sum;
}
```

# Source

https://codefights.com/arcade/intro/level-2/xskq4ZxLyqQMCLshr



# References

https://chortle.ccsu.edu/java5/Notes/chap49C/ch49C_1.html



---

created at 2017-04-04 00:01