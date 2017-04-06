# Description

Given a sequence of integers as an array, determine whether it is possible to obtain a strictly increasing sequence by removing no more than one element from the array.

**Example**

- For `sequence = [1, 3, 2, 1]`, the output should be
  `almostIncreasingSequence(sequence) = false`;

  There is no one element in this array that can be removed in order to get a strictly increasing sequence.

- For `sequence = [1, 3, 2]`, the output should be
  `almostIncreasingSequence(sequence) = true`.

  You can remove `3` from the array to get the strictly increasing sequence `[1, 2]`. Alternately, you can remove `2` to get the strictly increasing sequence `[1, 3]`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer sequence**

  *Guaranteed constraints:*
  `2 ≤ sequence.length ≤ 10^5`$10^5$,
  `-10^5 ≤ sequence[i] ≤ 10^5`.

- **[output] boolean**

  Return `true` if it is possible to remove one element from the array in order to get a strictly increasing sequence, otherwise return `false`.

# Solutions

**Best Votes**

``` java
boolean almostIncreasingSequence(int[] sequence) {
    int numErr = 0;
    for (int i = 0; i < sequence.length - 1; i++) {
        if (sequence[i] - sequence[i+1] >= 0) {
            numErr += 1;
            if (i - 1 >= 0 && i + 2 <= sequence.length - 1
               && sequence[i] - sequence[i+2] >= 0
               && sequence[i-1] - sequence[i+1] >= 0) {
                return false;
            }
        }
    }
    
    return numErr <= 1;
}
```

**Mine**

``` java
/*
Step1: if previous greater than next occurs twices, return false;
Step2: if once, then remove the previous and the next individually,if the previous of removed index less than the next of removed index,return true. else return false.
*/
public boolean almostIncreasingSequence(int[] sequence){
    int previousNotLtNext = 0;
    int previousGtNextIndex = -1;
    for(int i=0; i<sequence.length-1; i++){
        if(sequence[i] >= sequence[i+1]){
            previousGtNextIndex = i;
            previousNotLtNext++;
            if(previousNotLtNext > 1){
                return false;
            }
        }
    }

    if(previousNotLtNext < 1){
        return true;
    }

    /*
        1,3,2
        1,3,2,4
        1,3,1,4
        1,5,2,4
        1,2,3,4
     */
    //if previousGtNextIndex is firstIndex or sequence.length-2
    if(previousGtNextIndex==0 || previousGtNextIndex==(sequence.length-2) ){
        return true;
    }
    //if removedIndex is previous
    if(sequence[previousGtNextIndex-1] < sequence[previousGtNextIndex+1]){
        return true;
    }
    //if removedIndex is next
    if(sequence[previousGtNextIndex] < sequence[previousGtNextIndex+2]){
        return true;
    }
    return false;
}
```

> V1. exceed the execution time limit

``` java
boolean almostIncreasingSequence(int[] sequence){
    if(sequence.length == 2){
        return true;
    }
    for(int i=0; i<sequence.length; i++){
        int start = (i!=0) ? 0 : i+1;
        int end = (i!=sequence.length-1) ? sequence.length-1 : sequence.length-2;

        boolean increased = true;
        int index = start;
        while (index < end){
            if(index == i){
                index++;
            }
            int s = index;
            index++;
            if(index == i){
                index++;
            }
            int e = index;

            if( (sequence[e] - sequence[s])<1 ){
                increased = false;
                break;
            }
        }

        if(increased){
            System.out.println("remove index is " + i);
            return true;
        }
    }
    return false;
}
```

# Source

https://codefights.com/arcade/intro/level-2/2mxbGwLzvkTCKAJMG



---

created at 2017-04-02 22:05

updated at 2017-04-03 23:00