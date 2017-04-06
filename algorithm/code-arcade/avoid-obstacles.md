# Description

You are given an array of integers representing coordinates of obstacles situated on a straight line.

Assume that you are jumping from the point with coordinate `0` to the right. You are allowed only to make jumps of the same length represented by some integer.

Find the minimal length of the jump enough to avoid all the obstacles.

**Example**

For `inputArray = [5, 3, 6, 7, 9]`, the output should be
`avoidObstacles(inputArray) = 4`.

Check out the image below for better understanding:

![img](https://codefightsuserpics.s3.amazonaws.com/tasks/avoidObstacles/img/example.png?_tm=1490625560816)

https://codefightsuserpics.s3.amazonaws.com/tasks/avoidObstacles/img/example.png

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer inputArray**

  Non-empty array of positive integers.

  *Guaranteed constraints:*
  `2 ≤ inputArray.length ≤ 10`,
  `1 ≤ inputArray[i] ≤ 40`.

- **[output] integer**

  The desired length.

# Solutions

**Best Votes**

``` java
int avoidObstacles(int[] inputArray) {
    int jump = 1;
    boolean fail = true;    
    while(fail) {
        jump++;
        fail = false;
        for(int i=0; i<inputArray.length; i++)
            if(inputArray[i]%jump==0) {
                fail = true;
                break;
            }
    }    
    return jump;
}
```

**Mine**

``` java
int avoidObstacles(int[] inputArray) {
    List<Integer> list = new ArrayList();
    for(int i : inputArray){
        list.add(i);
    }
    Collections.sort(list); // call Arrays.sort() using binary sort.
    int minimal = list.get(0);
    int maximal = list.get(list.size()-1);
    
    int jumpLength = 2;
    boolean loop = true;
    while(loop){
        loop = false;
        for(Integer i : list){
            if(i%jumpLength == 0){
                jumpLength++;
                loop = true;
                break;
            }
        }
    }
    return jumpLength;
}
```

# Source

https://codefights.com/arcade/intro/level-5/XC9Q2DhRRKQrfLhb5

---

created at 2017-04-06 19:00