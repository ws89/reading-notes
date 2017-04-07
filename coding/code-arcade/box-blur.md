# Description

Last night you had to study, but decided to party instead. Now there is a black and white photo of you that is about to go viral. You cannot let this ruin your reputation, so you want to apply *box blur* algorithm to the photo to hide its content.

The algorithm works as follows: each pixel `x` in the resulting image has a value equal to the average value of the input image pixels' values from the `3 × 3` square with the center at `x`. All pixels at the edges are cropped.

As pixel's value is an integer, all fractions should be rounded down.

**Example**

For

```
image = [[1, 1, 1], 
         [1, 7, 1], 
         [1, 1, 1]]

```

the output should be `boxBlur(image) = [[1]]`.

In the given example all boundary pixels were cropped, and the value of the pixel in the middle was obtained as `(1 + 1 + 1 + 1 + 7 + 1 + 1 + 1 + 1) / 9 = 15 / 9 = *~rounded down~* = 1`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.array.integer image**

  An image is stored as a rectangular matrix of non-negative integers.

  *Guaranteed constraints:*
  `3 ≤ image.length ≤ 10`,
  `3 ≤ image[0].length ≤ 10`,
  `0 ≤ image[i][j] ≤ 255`.

- **[output] array.array.integer**

  A blurred image.

# Solutions

**Best Votes**

``` java

```

**Mine**

``` java
int[][] boxBlur(int[][] image) {
    int row = image.length - 2;
    int column = image[0].length - 2;
    int[][] blurBox = new int[row][column];
    
    for(int i=2; i<image.length; i++){
        for(int j=2;j<image[0].length; j++){
            int sum = 0;
            int count = 1;
            int rowEnd = i;
            int columnEnd = j;
            while(count < 4){
                sum = sum + image[rowEnd][columnEnd] + image[rowEnd][columnEnd-1] + image[rowEnd][columnEnd-2];
                rowEnd--;
                count++;
            }
            blurBox[i-2][j-2] = sum/9;        
        }
    }
     return blurBox;
}
```

# Source

https://codefights.com/arcade/intro/level-5/5xPitc3yT3dqS7XkP

---

created at 2017-04-07 21:00 