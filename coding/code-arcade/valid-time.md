# Description

Check if the given string is a correct time representation of the 24-hour clock.

**Example**

- For `time = "13:58"`, the output should be
  `validTime(time) = true`;
- For `time = "25:51"`, the output should be
  `validTime(time) = false`;
- For `time = "02:76"`, the output should be
  `validTime(time) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string time**

  A string representing time in `HH:MM` format. It is guaranteed that the first two characters, as well as the last two characters, are digits.

- **[output] boolean**

  `true` if the given representation is correct, `false` otherwise.



# Solutions

**Best Votes**

``` java
boolean validTime(String time) {
    String[] parts = time.split(":");
    
    if( Integer.parseInt(parts[0]) >= 24 )return false;
    if( Integer.parseInt(parts[1]) >= 60 )return false;
    return true;
}
```

**Mine**

``` java
boolean validTime(String time) {
    String[] array = time.split(":");
    int part1 = Integer.parseInt(array[0]);
    int part2 = Integer.parseInt(array[1]);
    return part1>=0 && part1<=23 && part2>=0 && part2<=59;
}
```

# Source

https://codefights.com/arcade/intro/level-12/ywMyCTspqGXPWRZx5

---

created at 2017-04-20 23:02 