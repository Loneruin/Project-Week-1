What issues will you address by cleaning the data?
Because of the limited of time. Here are some of the issues I have found in the data
1. Wrong price for the product: the price has to be divided by 1000000 to have the real price
2. Null values and columns without any data
3. Duplicate records: find the unique
4. the all_sessions table file may be splitted into some smaller table which helps the reader easily to understand
5. Inconsistent between Uppercase and Lowercase in header. For instance, in all_sessions table, the visitor Id was written as "fullVisitorId" while in analytics table, it was written as "fullvisitorid"



Queries:
Below, provide the SQL queries you used to clean your data.
1. Wrong price for the product price:
```SQL
SELECT ROUND("productPrice")/1000000
FROM all_sessions
```
2. Duplicate records

3. Inconsisten between Uppercase and Lowercase in header

```SQL
SELECT LOWER(a."fullVisitorId") as fullvisitorid,
			"country"
FROM all_sessions a
JOIN analytics b
ON a."fullVisitorId" = b."fullvisitorid"
GROUP BY a."fullVisitorId",
		"country",
		b."fullvisitorid"
ORDER BY "fullvisitorid"
```

