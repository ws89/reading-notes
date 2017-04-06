# Description

Given a year, return the century it is in. The first century spans from the year 1 up to and including the year 100, the second - from the year 101 up to and including the year 200, etc.

**Example**

- For `year = 1905`, the output should be
  `centuryFromYear(year) = 20`;
- For `year = 1700`, the output should be
  `centuryFromYear(year) = 17`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer year**

  A positive integer, designating the year.

  *Guaranteed constraints:*
  `1 ≤ year ≤ 2005`.

- **[output] integer**

  The number of the century the year is in.

# Solutions

**Best Votes**

``` java
int centuryFromYear(int year) {
    return 1 + (year - 1) / 100;
}
```



Mine

``` java
int centuryFromYear(int year) {
    double d = year/100.0;
    int dToInt = (int)d;
    if( d > dToInt ){
        return ++dToInt;
    }else{
        return dToInt;
    }
}
```

# Source

https://codefights.com/arcade/intro/level-1/egbueTZRRL5Mm4TXN



------

created at 2017-04-01 17:40