# Description

You are taking part in an Escape Room challenge designed specifically for programmers. In your efforts to find a clue, you've found a binary code written on the wall behind a vase, and realized that it must be an encrypted message. After some thought, your first guess is that each consecutive `8` bits of the code stand for the character with the corresponding [extended ASCII code](http://www.ascii-code.com/).

Assuming that your hunch is correct, decode the message.

**Example**

For `code = "010010000110010101101100011011000110111100100001"`, the output should be
`messageFromBinaryCode(code) = "Hello!"`.

The first `8` characters of the code are `01001000`, which is `72` in the binary numeral system. `72` stands for `H` in the *ASCII-table*, so the first letter is `H`.
Other letters can be obtained in the same manner.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string code**

  A string, the encrypted message consisting of characters `'0'` and `'1'`.

  *Guaranteed constraints:*
  `0 < code.length < 800`.

- **[output] string**

  The decrypted message.



# Solutions

**Best Votes**

``` java
String messageFromBinaryCode(String code) {
    String res = "";
    while (code.length() > 0) {
        int c = Integer.parseInt(code.substring(0,8), 2);
        res += (char) c;
        
        code = code.substring(8);
    }
    
    return res;
}
```

**Mine**

``` java
String messageFromBinaryCode(String code) {
    StringBuilder sb = new StringBuilder();
    for(int i=0; i<code.length(); i+=8 ){
        int t = Integer.parseInt( code.substring(i,i+8) ,2 );
        sb.append( (char)t );
    }
    return sb.toString();
}
```

# Source

https://codefights.com/arcade/intro/level-12/sCpwzJCyBy2tDSxKW

---

created at 2017-04-27 22:00