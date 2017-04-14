# Description

Each day a plant is growing by `upSpeed` meters. Each night that plant's height decreases by `downSpeed` meters due to the lack of sun heat. Initially, plant is 0 meters tall. We plant the seed at the beginning of a day. We want to know when the height of the plant will reach a certain level.

**Example**

For `upSpeed = 100`, `downSpeed = 10` and `desiredHeight = 910`, the output should be
`growingPlant(upSpeed, downSpeed, desiredHeight) = 10`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer upSpeed**

  A positive integer representing the daily growth.

  *Guaranteed constraints:*
  `5 ≤ upSpeed ≤ 100`.

- **[input] integer downSpeed**

  A positive integer representing the nightly decline.

  *Guaranteed constraints:*
  `2 ≤ downSpeed < upSpeed`.

- **[input] integer desiredHeight**

  A positive integer representing the threshold.

  *Guaranteed constraints:*
  `4 ≤ desiredHeight ≤ 1000`.

- **[output] integer**

  The number of days that it will take for the plant to reach/pass `desiredHeight` (including the last day in the total count).

# Solutions

**Best Votes**

``` java
int growingPlant(int upSpeed, int downSpeed, int desiredHeight)
{
    // The height after x days (up - down) * (x - 1) + up
    // We want dh <= (u - d) * (x - 1) + u
    // (dh - u) / (u - d) + 1 <= x
    return Math.max(0, (int) Math.ceil((double) (desiredHeight - upSpeed) / (upSpeed - downSpeed))) + 1;
}
```

**Mine**

``` java
int growingPlant(int upSpeed, int downSpeed, int desiredHeight) {
    int i = 1;
    int sum = 0;
    while(true){
        sum += upSpeed;
        if(sum >= desiredHeight){
            break;
        }
        sum -= downSpeed;
        i++;
    }
    return i;
}
```

# Source

https://codefights.com/arcade/intro/level-9/xHvruDnQCx7mYom3T

---

created at 2017-04-14 19:25