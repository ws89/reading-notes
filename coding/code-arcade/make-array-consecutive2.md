# Description

Ratiorg got `statues` of *different* sizes as a present from CodeMaster for his birthday, each statue having an non-negative integer size. Since he likes to make things perfect, he wants to arrange them from smallest to largest so that each statue will be bigger than the previous one exactly by `1`. He may need some additional statues to be able to accomplish that. Help him figure out the minimum number of additional statues needed.

**Example**

For `statues = [6, 2, 3, 8]`, the output should be
`makeArrayConsecutive2(statues) = 3`.

Ratiorg needs statues of sizes `4`, `5` and `7`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer statues**

  An array of *distinct* non-negative integers.

  *Guaranteed constraints:*
  `1 ≤ statues.length ≤ 10`,
  `0 ≤ statues[i] ≤ 20`.

- **[output] integer**

  The minimal number of statues that need to be added to existing `statues` such that it contains every integer size from an interval `[L, R]` (for some `L, R`) and no other sizes.

# Solutions

**Best Votes**

``` java
int makeArrayConsecutive2(int[] a) {
    Arrays.sort(a);
    int n = a[a.length-1] - a[0];
    return n - a.length + 1;
}
```

**Mine**

``` java
int makeArrayConsecutive2(int[] statues) {
    sort(statues);
    int sum = 0;
    for(int i=0; i<statues.length-1; i++){          
        sum += (statues[i+1] - statues[i] -1);        
    }
    return sum;
}

//sorted the array from small to large
void sort(int[] array){
    int temp = 0;
    for(int i=0;i<array.length-1;i++){
        for(int j=(i+1); j<array.length; j++ ){
            if(array[i] > array[j]){
                temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
    }
}
```

# Source

https://codefights.com/arcade/intro/level-2/bq2XnSr5kbHqpHGJC/solutions



---

created at 2017-04-02 01:16