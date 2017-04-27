# Description

Construct a square matrix with a size `N × N` containing integers from `1` to `N * N` in a spiral order, starting from top-left and in clockwise direction.

**Example**

For `n = 3`, the output should be

```
spiralNumbers(n) = [[1, 2, 3],
                    [8, 9, 4],
                    [7, 6, 5]]

```

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  Matrix size, a positive integer.

  *Guaranteed constraints:*
  `3 ≤ n ≤ 10`.

- **[output] array.array.integer**



# Solutions

**Best Votes**

``` java
int[][] spiralNumbers(int n) {
    int[][] array = new int[n][n];
    
    int left = 0;
    int right = n - 1;
    int top = 0;
    int down = n - 1;
    
    for (int counter = 1; counter <= n * n; )
    {
        for (int x = left; x <= right; x++)
            array[top][x] = counter++;
        top++;
        
        for (int y = top; y <= down; y++)
            array[y][right] = counter++;
        right--;
        
        for (int x = right; x >= left; x--)
            array[down][x] = counter++;
        down--;
        
        for (int y = down; y >= top; y--)
            array[y][left] = counter++;
        left++;
    }
    
    return array;
}
```

**Mine**

``` java
int[][] spiralNumbers(int n) {
    int[][] matrix = new int[n][n];

    int row = 0;
    int column = 0;
    int count = 0;

    int columnEnd = n-1;
    int rowEnd = n-1;
    int columnStart = 0;
    int rowStart = 0;

    boolean columnUp = true;
    boolean columnDown = false;
    boolean rowUp = false;
    boolean rowDown = false;

    while(count < n*n){
        count++;
        matrix[row][column] = count;

        boolean rowFixed = !rowUp && !rowDown;
        boolean columnFixed = !columnUp && !columnDown;

        if( columnUp && rowFixed){
            column++;
        }
        if( columnDown && rowFixed ){
            column--;
        }
        if( rowUp && columnFixed){
            row++;
        }
        if( rowDown && columnFixed ){
            row--;
        }


        if(columnUp && column>=columnEnd){
            rowStart++;
            columnUp = false;
            columnDown = false;
            rowUp = true;
            rowDown = false;
        }
        if(columnDown && column <= columnStart){
            rowEnd--;
            columnUp = false;
            columnDown = false;
            rowDown = true;
            rowUp = false;
        }
        if(row >= rowEnd && rowUp){
            columnEnd--;
            rowUp = false;
            rowDown = false;
            columnDown = true;
            columnUp = false;
        }
        if(row <= rowStart && rowDown){
            columnStart++;
            rowUp = false;
            rowDown = false;
            columnUp = true;
            columnDown = false;
        }

    }

    return matrix;
}
```

# Source

https://codefights.com/arcade/intro/level-12/uRWu6K8E7uLi3Qtvx

---

created at 2017-04-27 22:00