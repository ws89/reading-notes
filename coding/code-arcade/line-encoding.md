# Description

Given a string, return its encoding defined as follows:

- First, the string is divided into the least possible number of disjoint [substrings](keyword://substring) consisting of identical charactersfor example, `"aabbbc"` is divided into `["aa", "bbb", "c"]`
- Next, each *substring* with length greater than one is replaced with a concatenation of its length and the repeating characterfor example, *substring* `"bbb"` is replaced by `"3b"`
- Finally, all the new strings are concatenated together in the same order and a new string is returned.

**Example**

For `s = "aabbbc"`, the output should be
`lineEncoding(s) = "2a3bc"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string s**

  String consisting of lowercase English letters.

  *Guaranteed constraints:*
  `4 ≤ s.length ≤ 15`.

- **[output] string**

  Encoded version of `s`.



# Solutions

**Best Votes**

``` java
String lineEncoding(String s) {
    Matcher m = Pattern.compile("([a-z])\\1*").matcher(s);
    String result = "";
    while(m.find()){
        int len = m.group(0).length();
        char cha = m.group(0).charAt(0);
        result += (len==1?"":len) + "" + cha;
    }
    return result;
}
```

**Mine**

``` java
String lineEncoding(String s) {
    char[] array = s.toCharArray();
    StringBuilder sb = new StringBuilder();
    int count = 0;
    char temp = array[0];
    for(int i=0; i<array.length; i++){
        char c = array[i];
        if(c == temp){
            count++;
            if(i < array.length-1){
                continue;
            }
        }

        if(count>1){
            sb.append(count);
        }
        sb.append(temp);
        
        //is last one
        if(i == array.length-1){
            if(c != temp){
                sb.append(c);
            }
            break;
        }        

        count = 1;
        temp = c;
    }

    return sb.toString();
}
```

# Source

https://codefights.com/arcade/intro/level-11/o2uq6eTuvk7atGadR

---

created at 2017-04-16 22:45