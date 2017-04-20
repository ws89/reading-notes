# Description

CodeMaster has just returned from shopping. He scanned the check of the items he bought and gave the resulting string to Ratiorg to figure out the total number of purchased items. Since Ratiorg is a bot he is definitely going to automate it, so he needs a program that sums up all the numbers which appear in the given input.

Help Ratiorg by writing a function that returns the sum of numbers that appear in the given `inputString`.

**Example**

For `inputString = "2 apples, 12 oranges"`, the output should be
`sumUpNumbers(inputString) = 14`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string inputString**

  *Guaranteed constraints:*
  `6 ≤ inputString.length ≤ 60`.

- **[output] integer**



# Solutions

**Best Votes**

``` java
int sumUpNumbers(String inputString) {
    Matcher m = Pattern.compile("\\d+").matcher(inputString);
    int sum = 0;
    while( m.find() ){
        sum += Integer.parseInt(m.group(0));
    }
    return sum;
}
```

**Mine**

``` java
int sumUpNumbers(String inputString) {
    String[] array = inputString.split("\\D+");
    int sum = 0;
    for(String s : array){        
        try{
            sum += Integer.parseInt(s);
        }catch(NumberFormatException e){
            System.out.println( s );
        }        
    }    
    return sum;
}
```

# Source

https://codefights.com/arcade/intro/level-12/YqZwMJguZBY7Hz84T

---

created at 2017-04-20 23:20