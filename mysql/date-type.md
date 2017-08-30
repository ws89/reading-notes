# DATE, DATETIME, and TIMESTAMP

The `DATE` type is used for values with a date part but no time part. MySQL retrieves and displays `DATE` values in `'YYYY-MM-DD'` format. The supported range is `'1000-01-01'` to `'9999-12-31'`.



The `DATETIME` type is used for values that contain both date and time parts. MySQL retrieves and displays `DATETIME` values in `'YYYY-MM-DD HH:MM:SS'` format. The supported range is  `'1000-01-01 00:00:00'` to `'9999-12-31 23:59:59'`.
   

The `TIMESTAMP` data type is used for values that contain both date and time parts. `TIMESTAMP` has a range of `'1970-01-01 00:00:01'` **UTC** to `'2038-01-19 03:14:07'` UTC.   

A `DATETIME` or `TIMESTAMP` value can include a trailing fractional seconds part in up to **microseconds (6 digits)** precision. In particular, any fractional part in a value inserted into a `DATETIME` or `TIMESTAMP` column is stored rather than discarded. With the fractional part included, the format for these values is `'YYYY-MM-DD HH:MM:SS[.fraction]'`, the range for `DATETIME` values is `'1000-01-01 00:00:00.000000'` to `'9999-12-31 23:59:59.999999'`, and the range for `TIMESTAMP` values is `'1970-01-01 00:00:01.000000'` to `'2038-01-19 03:14:07.999999'`. 

**Example**

```sql
CREATE TABLE `test_table` (
  `id` bigint(20) NOT NULL,
  `int_test` int(11) unsigned NOT NULL DEFAULT '0',
  `date_test` timestamp(5) NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

```sql
mysql> CREATE TABLE fractest( c1 TIME(2), c2 DATETIME(2), c3 TIMESTAMP(2) );
mysql> INSERT INTO fractest VALUES
  > ('17:51:04.777', '2014-09-08 17:51:04.777', '2014-09-08 17:51:04.777');
  
mysql> SELECT * FROM fractest;
+-------------+------------------------+------------------------+
| c1   | c2    | c3    |
+-------------+------------------------+------------------------+
| 17:51:04.78 | 2014-09-08 17:51:04.78 | 2014-09-08 17:51:04.78 |
+-------------+------------------------+------------------------+  
  
```

```sql
select now(3);

'2017-08-30 15:23:46.736'
```



## TIMESTATMP

 MySQL converts `TIMESTAMP` values from the current time zone to UTC for storage, and back from UTC to the current time zone for retrieval. (This does not occur for other types such as `DATETIME`.) By default, the current time zone for each connection is the server's time. The time zone can be set on a per-connection basis. As long as the time zone setting remains constant, you get back the same value you store. If you store a `TIMESTAMP` value, and then change the time zone and retrieve the value, the retrieved value is different from the value you stored. 



# Date value interpretation

 Invalid `DATE`, `DATETIME`, or `TIMESTAMP` values are converted to the “zero” value of the appropriate type (`'0000-00-00'` or `'0000-00-00 00:00:00'`).   

 Be aware of certain properties of date value interpretation in MySQL:

- MySQL permits a “relaxed” format for values  specified as strings, in which any punctuation character may  be used as the delimiter between date parts or time parts.  In some cases, this syntax can be deceiving. For example, a  value such as `'10:11:12'` might look like  a time value because of the `:`, but is  interpreted as the year `'2010-11-12'` if  used in a date context. The value  `'10:45:15'` is converted to  `'0000-00-00'` because  `'45'` is not a valid month.   

  The only delimiter recognized between a date and time part  and a fractional seconds part is the decimal point.   

- The server requires that month and day values be valid, and  not merely in the range 1 to 12 and 1 to 31, respectively.  With strict mode disabled, invalid dates such as  `'2004-04-31'` are converted to  `'0000-00-00'` and a warning is generated.  With strict mode enabled, invalid dates generate an error.  To permit such dates, enable  [`ALLOW_INVALID_DATES`](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_allow_invalid_dates). See  [Section 5.1.8, “Server SQL Modes”](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html), for more information.   

- MySQL does not accept `TIMESTAMP` values  that include a zero in the day or month column or values  that are not a valid date. The sole exception to this rule  is the special “zero” value  `'0000-00-00 00:00:00'`.   

- Dates containing two-digit year values are ambiguous because  the century is unknown. MySQL interprets two-digit year  values using these rules:

  - Year values in the range `00-69` are  converted to `2000-2069`.    
  -  Year values in the range `70-99` are  converted to `1970-1999`.



# References

[11.3.1 The DATE, DATETIME, and TIMESTAMP Types](https://dev.mysql.com/doc/refman/5.7/en/datetime.html)

[5.1.8 Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html)

[sql_mode](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sql_mode)

[11.3.6 Fractional Seconds in Time Values](https://dev.mysql.com/doc/refman/5.7/en/fractional-seconds.html)

[11.3.2 The TIME Type](https://dev.mysql.com/doc/refman/5.7/en/time.html)

---

created at 2017-08-30 14:05