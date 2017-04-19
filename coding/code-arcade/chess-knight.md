# Description

Given a position of a knight on the standard chessboard, find the number of different moves the knight can perform.

The knight can move to a square that is two squares horizontally and one square vertically, or two squares vertically and one square horizontally away from it. The complete move therefore looks like the letter L. Check out the image below to see all valid moves for a knight piece that is placed on one of the central squares.

![img](https://codefightsuserpics.s3.amazonaws.com/tasks/chessKnight/img/knight.jpg?_tm=1486560102464)

**Example**

- For `cell = "a1"`, the output should be
  `chessKnight(cell) = 2`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/chessKnight/img/ex_1.jpg?_tm=1486560102718)

- For `cell = "c2"`, the output should be
  `chessKnight(cell) = 6`.

  ![img](https://codefightsuserpics.s3.amazonaws.com/tasks/chessKnight/img/ex_2.jpg?_tm=1486560102902)

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string cell**

  String consisting of 2 letters - coordinates of the knight on an `8 × 8` chessboard in [chess notation](keyword://chess-notation).

- **[output] integer**



# Solutions

**Best Votes**

``` java
int chessKnight(String cell) {

    char c1 = cell.charAt(0), c2 = cell.charAt(1);
    int cnt = 0;
    
    //left
    if(c1-2>='a' && c2+1<='8')
        cnt++;
    if(c1-2>='a' && c2-1>='1')
        cnt++;
    //right
    if(c1+2<='h' && c2+1<='8')
        cnt++;
    if(c1+2<='h' && c2-1>='1')
        cnt++;
    //up
    if(c1-1>='a' && c2+2<='8')
        cnt++;
    if(c1+1<='h' && c2+2<='8')
        cnt++;
    //down
    if(c1-1>='a' && c2-2>='1')
        cnt++;
    if(c1+1<='h' && c2-2>='1')
        cnt++;
    
    return cnt;
}
```

**Mine**

``` java
int chessKnight(String cell) {
    char cx = cell.charAt(0);
    char cy = cell.charAt(1);
    
    int count = 0;
    
    
    char cyTemp = (char)(cy-1);
    
    char cxTemp = (char)(cx-2);
    if( isXValid(cxTemp) ){
        cyTemp = (char)(cy-1);
        if( isYValid(cyTemp) ){
            count++;
        }
        cyTemp = (char)(cy+1);
        if( isYValid(cyTemp) ){
            count++;
        }
    }
    
    cxTemp = (char)(cx+2);
    if( isXValid(cxTemp) ){
        cyTemp = (char)(cy-1);
        if( isYValid(cyTemp) ){
            count++;
        }
        cyTemp = (char)(cy+1);
        if( isYValid(cyTemp) ){
            count++;
        }
    }
    
    cxTemp = (char)(cx-1);
    if( isXValid(cxTemp) ){
        cyTemp = (char)(cy-2);
        if( isYValid(cyTemp) ){
            count++;
        }
        cyTemp = (char)(cy+2);
        if( isYValid(cyTemp) ){
            count++;
        }
    }
    
    cxTemp = (char)(cx+1);
    if( isXValid(cxTemp) ){
        cyTemp = (char)(cy-2);
        if( isYValid(cyTemp) ){
            count++;
        }
        cyTemp = (char)(cy+2);
        if( isYValid(cyTemp) ){
            count++;
        }
    }
    
    return count;
}

boolean isXValid(char cx){
    return cx>='a' && cx<= 'h';
}
boolean isYValid(char cy){
    return cy>='1' && cy<='8';
}
```

# Source

https://codefights.com/arcade/intro/level-11/pwRLrkrNpnsbgMndb

---

created at 2017-04-19 20:00