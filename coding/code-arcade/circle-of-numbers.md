# Description

Consider integer numbers from `0` to `n - 1` written down along the circle in such a way that the distance between any two neighbouring numbers is equal (note that `0` and `n - 1` are neighbouring, too).

Given `n` and `firstNumber`, find the number which is written in the radially opposite position to `firstNumber`.

**Example**

For `n = 10` and `firstNumber = 2`, the output should be
`circleOfNumbers(n, firstNumber) = 7`.

![img](https://codefightsuserpics.s3.amazonaws.com/tasks/circleOfNumbers/img/example.png?_tm=1490625697098)

https://codefightsuserpics.s3.amazonaws.com/tasks/circleOfNumbers/img/example.png

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer n**

  A positive **even** integer.

  *Guaranteed constraints:*
  `4 ≤ n ≤ 20`.

- **[input] integer firstNumber**

  *Guaranteed constraints:*
  `0 ≤ firstNumber ≤ n - 1`.

- **[output] integer**

# Solutions

**Best Votes**

``` java
int circleOfNumbers(int n, int firstNumber) {
    return (firstNumber + n / 2) % n;
}
```

**Mine**

``` java
int circleOfNumbers(int n, int firstNumber) {
    if(n/2 > firstNumber){
      return firstNumber + n/2;
    }else{
      return firstNumber - n/2;
    }
}
```

# Source

https://codefights.com/arcade/intro/level-7/vExYvcGnFsEYSt8nQ

---

created at 2017-04-10 13:00 