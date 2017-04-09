# Description

Given two cells on the standard chess board, determine whether they have the same color or not.

**Example**

- For `cell1 = "A1"` and `cell2 = "C3"`, the output should be
  `chessBoardCellColor(cell1, cell2) = true`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/chessBoardCellColor/img/example1.png?_tm=1490451398494)

  https://codefightsuserpics.s3.amazonaws.com/tasks/chessBoardCellColor/img/example1.png

- For `cell1 = "A1"` and `cell2 = "H3"`, the output should be
  `chessBoardCellColor(cell1, cell2) = false`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/chessBoardCellColor/img/example2.png?_tm=1490451398630)

https://codefightsuserpics.s3.amazonaws.com/tasks/chessBoardCellColor/img/example2.png

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string cell1**


- **[input] string cell2**


- **[output] boolean**

  `true` if both cells have the same color, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean chessBoardCellColor(String cell1, String cell2) {
    return Math.abs(cell1.charAt(0) - cell2.charAt(0)) % 2 == Math.abs(cell1.charAt(1) - cell2.charAt(1)) % 2;
}
```

**Mine**

``` java
boolean chessBoardCellColor(String cell1, String cell2) {
    char c1f = cell1.charAt(0);
    char c1s = cell1.charAt(1);
    
    char c2f = cell2.charAt(0);
    char c2s = cell2.charAt(1);
    
    return ( (c2f-c1f)%2==0 && (c2s-c1s)%2==0 )
             ||
         ( (c2f-c1f)%2!=0 && (c2s-c1s)%2!=0 );
}
```

# Source

https://codefights.com/arcade/intro/level-6/t97bpjfrMDZH8GJhi

---

created at 2017-04-10 00:23 