# String Substitution

By default, using the `#{}` syntax will cause MyBatis to generate `PreparedStatement` properties and set the values safely against the `PreparedStatement` parameters (e.g. ?). While this is safer, faster and almost always preferred, sometimes you just want to directly inject an unmodified string into the SQL Statement. For example, for ORDER BY, you might use something like this:        

```
ORDER BY ${columnName}
```

Here MyBatis won't modify or escape the string.        

NOTE: It's not safe to accept input from a user and supply it to a statement unmodified in this way. This leads to potential **SQL Injection** attacks and therefore you should either disallow user input in these fields, or always perform your own escapes and checks.        

# References

[String Substitution](http://www.mybatis.org/mybatis-3/sqlmap-xml.html)

---

created at 2017-08-29 15:41