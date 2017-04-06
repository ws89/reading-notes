# Description

Several people are standing in a row and need to be divided into two teams. The first person goes into *team 1*, the second goes into *team 2*, the third goes into *team 1* again, the fourth into *team 2*, and so on.

You are given an array of positive integers - the weights of the people. Return an array of two integers, where the first element is the total weight of *team 1*, and the second element is the total weight of *team 2* after the division is complete.

**Example**

For `a = [50, 60, 60, 45, 70]`, the output should be
`alternatingSums(a) = [180, 105]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer a**

  *Guaranteed constraints:*
  `1 ≤ a.length ≤ 10`,
  `45 ≤ a[i] ≤ 100`.

- **[output] array.integer**

# Solutions

**Best Votes**

``` java
int[] alternatingSums(int[] a) {
    int []niz = new int[2];  
    for(int i=0; i<a.length; i++)
        niz[i%2]+=a[i];
    
    return niz;
}
```

**Mine**

``` java
int[] alternatingSums(int[] a) {
    int team1 = 0;
    int team2 = 0;
    
    int i = 1;
    while( i <= a.length ){
        if(i%2 == 0){
            team2 += a[i-1];
        }else{
            team1 += a[i-1];
        }
        i++;
    }
    return new int[]{team1,team2};
}
```

# Source

https://codefights.com/arcade/intro/level-4/cC5QuL9fqvZjXJsW9

---

created at 2017-04-06 15:11