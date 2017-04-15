# Description

Elections are in progress!

Given an array of the numbers of votes given to each of the candidates so far, and an integer `k` equal to the number of voters who haven't cast their vote yet, find the number of candidates who still have a chance to win the election.

The winner of the election must secure strictly more votes than any other candidate. If two or more candidates receive the same (maximum) number of votes, assume there is no winner at all.

**Example**

For `votes = [2, 3, 5, 2]` and `k = 3`, the output should be
`electionsWinners(votes, k) = 2`.

- The first candidate got `2` votes. Even if all of the remaining `3` candidates vote for him, he will still have only `5` votes, i.e. the same number as the third candidate, so there will be no winner.
- The second candidate can win if all the remaining candidates vote for him (`3 + 3 = 6 > 5`).
- The third candidate can win even if none of the remaining candidates vote for him. For example, if each of the remaining voters cast their votes for each of his opponents, he will still be the winner (the `votes` array will thus be `[3, 4, 5, 3]`).
- The last candidate can't win no matter what (for the same reason as the first candidate).

Thus, only `2` candidates can win (the second and the third), which is the answer.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] array.integer votes**

  A non-empty array of non-negative integers. Its `ith` element denotes the number of votes cast for the `ith` candidate.

  *Guaranteed constraints:*
  `4 ≤ votes.length ≤ 105`,
  `0 ≤ votes[i] ≤ 104`.

- **[input] integer k**

  The number of voters who haven't cast their vote yet.

  *Guaranteed constraints:*
  `0 ≤ k ≤ 105`.

- **[output] integer**



# Solutions

**Best Votes**

``` java
int electionsWinners(int[] votes, int k)
{
    int max = Arrays.stream(votes).max().getAsInt();
    return k == 0 ? Arrays.stream(votes).filter(x -> x == max).count() == 1 ? 1 : 0 : (int) Arrays.stream(votes).filter(x -> x + k > max).count();
}
```

``` java
int electionsWinners(int[] votes, int k) {
    int max = 0;
    for (int v : votes) if (max < v) max = v;
    
    int count = 0, maxCount = 0;
    for (int v : votes) {
        if (v == max) maxCount ++;
        if (v + k > max) count ++;
    }
    
    return k != 0 ? count : maxCount<2? 1 : 0;
}
```



**Mine**

``` java
int electionsWinners(int[] votes, int k) {
    TreeSet<Integer> set = new TreeSet();
    for(int temp : votes){
        set.add(temp);
    }

    int maximum = set.last();

    int count = 0;
    int equalCount = 0;
    for(int temp : votes){
        if(temp + k > maximum){
            count++;
        }
        if( k == 0 && temp + k == maximum){
            equalCount++;
        }
    }
    if(equalCount == 1){
        count++;
    }
    return count;
}
```

# Source

https://codefights.com/arcade/intro/level-10/8RiRRM3yvbuAd3MNg

---

created at 2017-04-15 17:00