# Description

Given the positions of a white bishop and a black pawn on the standard chess board, determine whether the bishop can capture the pawn in one move.

The bishop has no restrictions in distance for each move, but is 
limited to diagonal movement. Check out the example below to see how it 
can move:
![img](https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/bishop.jpg?_tm=1486560043503)

https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/bishop.jpg

**Example**

- For `bishop = "a1"` and `pawn = "c3"`, the output should be
  `bishopAndPawn(bishop, pawn) = true`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/ex1.jpg?_tm=1486560043684)

https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/ex1.jpg

- For `bishop = "h1"` and `pawn = "h3"`, the output should be
  `bishopAndPawn(bishop, pawn) = false`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/ex2.jpg?_tm=1486560043835)
  https://codefightsuserpics.s3.amazonaws.com/tasks/bishopAndPawn/img/ex2.jpg

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string bishop**

  Coordinates of the white bishop in the [chess notation](keyword://chess-notation).

- **[input] string pawn**

  Coordinates of the black pawn in the same notation.

- **[output] boolean**

  `true` if the bishop can capture the pawn, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean bishopAndPawn(String bishop, String pawn) {
    return(Math.abs(bishop.charAt(0) - pawn.charAt(0))) == (Math.abs(bishop.charAt(1) - pawn.charAt(1)));
}
```

**Mine**

``` java
boolean bishopAndPawn(String bishop, String pawn) {
    char b1 = bishop.charAt(0);
    char b2 = bishop.charAt(1);
    char p1 = pawn.charAt(0);
    char p2 = pawn.charAt(1);
    return Math.abs((b1-p1)) == Math.abs((b2-p2));
}
```

# Source

https://codefights.com/arcade/intro/level-9/6M57rMTFB9MeDeSWo

---

created at 2017-04-14 21:10 