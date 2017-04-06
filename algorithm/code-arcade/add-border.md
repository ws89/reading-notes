# Description

Given a rectangular matrix of characters, add a border of asterisks(`*`) to it.

**Example**

For

```
picture = ["abc",
           "ded"]

```

the output should be

```
addBorder(picture) = ["*****",
                      "*abc*",
                      "*ded*",
                      "*****"]
```

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.string picture**

  A non-empty array of non-empty equal-length strings.

  *Guaranteed constraints:*
  `1 ≤ picture.length ≤ 5`,
  `1 ≤ picture[i].length ≤ 5`.

- **[output] array.string**

  The same matrix of characters, framed with a border of asterisks of width `1`.

# Solutions

**Best Votes**

``` java
String[] addBorder(String[] picture) {
    String[] x = new String[picture.length + 2];
    char[] line = new char[picture[0].length() + 2];
    Arrays.fill(line,'*');
    x[0] = new String(line);
    x[x.length -1 ] =  x[0];
    for (int i = 1; i <= x.length - 2; ++i){
        x[i] = '*' + picture[i-1] + '*';
    }
    return x;
}
```

**Mine**

``` java
String[] addBorder(String[] picture) {
    String[] borderPicture = new String[picture.length+2];
    int stringLength = picture[0].length() + 2;
    
    boolean left = true,right = true;
    for(String s : picture){
        if( '*' != s.charAt(0) ){
            left = false;
        }
        if( '*' != s.charAt(s.length()-1) ){
            right = false;
        }
        if( !left && !right){
            break;
        }
    }
    if( left ){
        stringLength--;
    }
    if( right ){
        stringLength--;
    }
    
    StringBuilder sb = new StringBuilder();
    for(int i=0; i<stringLength; i++){
        sb.append("*");
    }
    
    borderPicture[0] = sb.toString();
    for(int i=0; i<picture.length; i++){
        StringBuilder tempSb = new StringBuilder(); 
        if( !left ){
            tempSb.append("*");
        }
        tempSb.append(picture[i]);
        if( !right ){
            tempSb.append("*");
        }
        borderPicture[i+1] = tempSb.toString();
    }
    borderPicture[borderPicture.length-1] = sb.toString();
    
    return borderPicture;
}

```

# Source

https://codefights.com/arcade/intro/level-4/ZCD7NQnED724bJtjN/solutions



---

created at 2017-04-06 16:00 