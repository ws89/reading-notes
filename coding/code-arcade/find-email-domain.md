# Description

An email address such as `"John.Smith@example.com"` is made up of a local part (`"John.Smith"`), an `"@"` symbol, then a domain part (`"example.com"`).

The domain name part of an email address may only consist of letters, digits, hyphens and dots. The local part, however, also allows a lot of different special characters. [Here](https://en.wikipedia.org/wiki/Email_address#Examples) you can look at several examples of correct and incorrect email addresses.

Given a valid email address, find its domain part.

**Example**

- For `address = "prettyandsimple@example.com"`, the output should be
  `findEmailDomain(address) = "example.com"`;
- For `address = "<>[]:,;@\"!#$%&*+-/=?^_{}| ~.a\"@example.org"`, the output should be
  `findEmailDomain(address) = "example.org"`.

**Input/Output**

- **[time limit] 3000ms (java)**


- **[input] string address**

  *Guaranteed constraints:*
  `10 ≤ address.length ≤ 50`.

- **[output] string**



# Solutions

**Best Votes**

``` java
String findEmailDomain(String address) {
    return address.substring(address.lastIndexOf("@") + 1);
}
```

**Mine**

``` java
String findEmailDomain(String address) {
    return address.substring( address.lastIndexOf("@")+1,address.length() );
}
```

# Source

https://codefights.com/arcade/intro/level-10/TXFLopNcCNbJLQivP

---

created at 2017-04-15 15:30