# Description

Call two arms *equally strong* if the heaviest weights they each are able to lift are equal.

Call two people *equally strong* if their strongest arms are equally strong (the strongest arm can be both the right and the left), and so are their weakest arms.

Given your and your friend's arms' lifting capabilities find out if you two are equally strong.

**Example**

- For `yourLeft = 10`, `yourRight = 15`, `friendsLeft = 15` and `friendsRight = 10`, the output should be
  `areEquallyStrong(yourLeft, yourRight, friendsLeft, friendsRight) = true`;
- For `yourLeft = 15`, `yourRight = 10`, `friendsLeft = 15` and `friendsRight = 10`, the output should be
  `areEquallyStrong(yourLeft, yourRight, friendsLeft, friendsRight) = true`;
- For `yourLeft = 15`, `yourRight = 10`, `friendsLeft = 15` and `friendsRight = 9`, the output should be
  `areEquallyStrong(yourLeft, yourRight, friendsLeft, friendsRight) = false`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer yourLeft**

  A non-negative integer representing the heaviest weight you can lift with your left arm.

  *Guaranteed constraints:*
  `0 ≤ yourLeft ≤ 15`.

- **[input] integer yourRight**

  A non-negative integer representing the heaviest weight you can lift with your right arm.

  *Guaranteed constraints:*
  `0 ≤ yourRight ≤ 15`.

- **[input] integer friendsLeft**

  A non-negative integer representing the heaviest weight your friend can lift with his or her left arm.

  *Guaranteed constraints:*
  `0 ≤ friendsLeft ≤ 15`.

- **[input] integer friendsRight**

  A non-negative integer representing the heaviest weight your friend can lift with his or her right arm.

  *Guaranteed constraints:*
  `0 ≤ friendsRight ≤ 15`.

- **[output] boolean**

  `true` if you and your friend are equally strong, `false` otherwise.

# Solutions

**Best Votes**

``` java
boolean areEquallyStrong(int yourLeft, int yourRight, int friendsLeft, int friendsRight) {
	return 	yourLeft==friendsLeft && yourRight==friendsRight || 
      		yourLeft==friendsRight && yourRight== friendsLeft;
}
```

**Mine**

``` java
boolean areEquallyStrong(int yourLeft, int yourRight, int friendsLeft, int friendsRight) {
  
    boolean b1 = yourLeft==friendsLeft || yourLeft==friendsRight;
    boolean b2 = yourRight==friendsRight || yourRight==friendsLeft;
    return b1 && b2;
}
```

# Source

https://codefights.com/arcade/intro/level-5/g6dc9KJyxmFjB98dL

---

created at 2017-04-06 18:05 