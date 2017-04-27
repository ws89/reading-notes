# Description

*Sudoku* is a number-placement puzzle. The objective is to fill a `9 × 9` grid with digits so that each column, each row, and each of the nine `3 × 3` sub-grids that compose the grid contains all of the digits from `1` to `9`.

This algorithm should check if the given grid of numbers represents a correct solution to Sudoku.

**Example**

For the first example below, the output should be `true`. For the other grid, the output should be `false`: each of the nine `3 × 3` sub-grids should contain all of the digits from `1` to `9`.

![img](https://codefightsuserpics.s3.amazonaws.com/tasks/sudoku/img/sudoku.png?_tm=1492959676075)

https://codefightsuserpics.s3.amazonaws.com/tasks/sudoku/img/sudoku.png



**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.array.integer grid**

  A matrix representing `9 × 9` grid already filled with numbers from `1` to `9`.

- **[output] boolean**

  `true` if the given grid represents a correct solution to Sudoku, `false` otherwise.



# Solutions

**Best Votes**

``` java

```

**Mine**

``` java
boolean sudoku(int[][] grid) {
    for(int i=0; i<grid.length; i+=3){
        for(int j=0; j<grid.length; j+=3){
            
            Set<Integer> set = new HashSet();
            for(int ii=i+0; ii<i+3; ii++){
                for(int jj=j+0; jj<j+3; jj++){
                    if( ! set.add( grid[ii][jj] ) ){
                        return false;
                    }
                }
            }          
            
        }
    }
    
    for(int i=0; i<grid.length; i++){
        Set<Integer> set = new HashSet();
        Set<Integer> set2 = new HashSet();
        for(int j=0; j<grid.length; j++){
            if( ! set.add( grid[i][j] ) ){
                return false;
            }
            if( ! set2.add(grid[j][i]) ){
                return false;
            }
        }
    }    
    
    return true;
}
```

# Source

https://codefights.com/arcade/intro/level-12/tQgasP8b62JBeirMS

---

created at 2017-04-28 01:30