# Description

Given an integer `product`, find the smallest **positive** integer the product of whose digits is equal to `product`. If there is no such integer, return `-1` instead.

**Example**

- For `product = 12`, the output should be
  `digitsProduct(product) = 26`;

  Beacause:` 12 = (2*6)  `

- For `product = 19`, the output should be
  `digitsProduct(product) = -1`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] integer product**

  *Guaranteed constraints:*
  `0 ≤ product ≤ 600`.

- **[output] integer**



# Solutions

**Best Votes**

``` java
int digitsProduct(int product) {
    int until = (int)Math.pow(10,Math.sqrt(product)+2);
    for(int i = 1 ; i < until ; i++){
        int res = 1;
        for( int n = i ; n > 0 ; n/=10){
            res *= n%10;
        }
        if(res == product) return i;
    }
    return -1;
}
```

```java
int digitsProduct(int product) {
    String outy = "";
    boolean found = false;
    int i=9; 

    if (product==0) 
        return 10;

    if (product ==1)
        return 1;

    while ((product>1) &&(i>1))
    {
            if (product%i==0)
            {
                outy+=i;
                product=product/i;
                found = true;
                i=9;
                System.out.println(outy);
            }
            else
                i--;
    }
  
    if (!found)
        return -1;
    String reverse = new StringBuffer(outy).reverse().toString();
    return Integer.parseInt(reverse);
}
```

**Mine**

``` java
int digitsProduct(int product) {
    if(product < 10 && product>0){
        return product;
    }
    if(product == 0){
        return 10;
    }
    
    int i = 9;
    while( i>1 ){
        List<Integer> list = new ArrayList();
        int result = recursion(product,list);
        
        if(result < 10){
            StringBuilder sb = new StringBuilder();
            Collections.sort(list);
            for(Integer t : list){
                sb.append(t);
            }
            return Integer.parseInt(sb.toString());
        }
        
        i--;
    }
    return -1;
}
// 90  9 5 2

int recursion(int number,List list){
    if(number < 10){
        list.add(number);
        return number;
    }
    int i=9;
    while(i > 1){
        if(number % i == 0){
            number /= i;
            list.add(i);
            return recursion(number,list);
        }
        i--;
    }
    return number;
}
```

# Source

https://codefights.com/arcade/intro/level-12/NJJhENpgheFRQbPRA

https://docs.oracle.com/javase/7/docs/api/java/lang/Math.html#sqrt

---

created at 2017-04-27 20:30