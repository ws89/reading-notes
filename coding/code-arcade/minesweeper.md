# Description

In the popular **Minesweeper** game you have a board with some mines and those cells that don't contain a mine have a number in it that indicates the total number of mines in the neighboring cells. Starting off with some arrangement of mines we want to create a **Minesweeper** game setup.

**Example**

For

```
matrix = [[true, false, false],
          [false, true, false],
          [false, false, false]]

```

the output should be

```
minesweeper(matrix) = [[1, 2, 1],
                       [2, 1, 1],
                       [1, 1, 1]]       

```

Check out the image below for better understanding:

![img](https://codefightsuserpics.s3.amazonaws.com/tasks/minesweeper/img/example.png?_tm=1490636350838)

https://codefightsuserpics.s3.amazonaws.com/tasks/minesweeper/img/example.png

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.array.boolean matrix**

  A non-empty rectangular matrix consisting of boolean values - `true` if the corresponding cell contains a mine, `false` otherwise.

  *Guaranteed constraints:*
  `2 ≤ matrix.length ≤ 5`,
  `2 ≤ matrix[0].length ≤ 5`.

- **[output] array.array.integer**

  Rectangular matrix of the same size as `matrix` each cell of which contains an integer equal to the number of mines in the neighboring cells. Two cells are called neighboring if they share at least one corner.

# Solutions

**Best Votes**

``` java
int[][] minesweeper(boolean[][] matrix) {
    int[][]res = new int[matrix.length][matrix[0].length];
    for (int i = 0; i < matrix.length; ++i) {
        for (int j = 0; j < matrix[0].length; ++j) {
            int s = 0;
            for (int ii = i - 1; ii <= i + 1; ++ ii) {
                for(int jj = j - 1; jj <= j + 1;  ++jj) {
                    if ((ii != i || jj != j) && (ii>=0 && jj >=0 && ii<matrix.length && jj< matrix[0].length)) 
                        if (matrix[ii][jj])
                            s++;
                }
            }
            res[i][j] = s;            
        }
    }
    return res;
}
```

**Mine**

``` java
int[][] minesweeper(boolean[][] matrix) {
    
    int row = matrix.length;
    int column = matrix[0].length;
    int[][] minesweeper = new int[row][column];
    
    for(int r=0; r<row; r++){
        for(int c=0; c<column; c++){
            boolean b = matrix[r][c];
            if(b){                
                if(r-1 >=0 ){
                    minesweeper[r-1][c]++;
                    
                    if(c-1 >= 0){
                        minesweeper[r-1][c-1]++;
                    }
                    if(c+1 < column){
                        minesweeper[r-1][c+1]++;
                    }
                }
                
                if(r+1 < row){
                    minesweeper[r+1][c]++;
                    
                    if(c-1 >=0){
                        minesweeper[r+1][c-1]++;
                    }
                    if(c+1 < column){
                        minesweeper[r+1][c+1]++;
                    }
                }
                
                if(c-1 >= 0){
                    minesweeper[r][c-1]++;
                }                
                if(c+1 < column){
                    minesweeper[r][c+1]++;
                }
            }
        }
    }
    return minesweeper;
}
```

# Source

https://codefights.com/arcade/intro/level-5/ZMR5n7vJbexnLrgaM

---

created at 2017-04-08 22:50 