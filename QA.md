What are your risk areas? Identify and describe them.
risk areas
Null value: data value contains plain information 
invalid value: data value doesn't provide any meaning in the analysis such as "(not set)", "not available in demo dataset", and "${escCatTitle}"
Duplicate value: values which are mistaking by inserting multiple times

QA Process:
Describe your QA process and include the SQL queries used to execute it.
1.Make sure the queries are readable
    .Uppercase for Keywords and Function
    . Use aliases for readability
    . Break long queries into multiple subqueries
```SQL
SELECT DISTINCT "productsku",
				"v2ProductName",
				"v2ProductCategory",
				a."total_ordered",
				"restockingLeadTime"
FROM("sales_report" a
	 JOIN "sales_by_sku" b
	 ON a."productsku" = b."productSKU")
JOIN all_sessions d
ON d."productSKU" = a."productsku"
WHERE "v2ProductCategory" <> '(not set)'AND
	"v2ProductCategory" <> '${escCatTitle}'
GROUP BY "productsku",
		"v2ProductName",
		"v2ProductCategory",
		a."total_ordered"
ORDER BY "total_ordered";
```
2. Using data assertion to looks for problems in a dataset. If the query returns any rows then the assertion fails. For instance:
```SQL
SELECT "fullVisitorId",
		country
FROM all_sessions
WHERE "fullVisitorId" IS NULL
```
3. Check numbers of rows between running query with and without DISTINCT function to compare the output. If they have a large gap between number of rows, we should have an investigation about the reason why behind. For example:
```SQL
SELECT  a."fullVisitorId",
		a."v2ProductCategory",
		a."v2ProductName",
		b."visitnumber",
		b."pageviews"
FROM all_sessions a
JOIN analytics b
ON	a."fullVisitorId" = b."fullvisitorid"
GROUP BY "fullVisitorId",
		"v2ProductCategory",
		a."v2ProductName",
		"visitnumber",
		b."pageviews"
ORDER BY "visitnumber" DESC,
		b."pageviews"

```
```SQL
SELECT DISTINCT a."fullVisitorId",
				a."v2ProductCategory",
				a."v2ProductName",
				b."visitnumber",
				b."pageviews"
FROM all_sessions a
JOIN analytics b
ON	a."fullVisitorId" = b."fullvisitorid"
GROUP BY "fullVisitorId",
		"v2ProductCategory",
		a."v2ProductName",
		"visitnumber",
		b."pageviews"
ORDER BY "visitnumber" DESC,
		b."pageviews"
```
4. Apply CONDITION function to omit mistaken and misleading values. Examples
```SQL
SELECT "productsku",
		"country",
		"city",
		"v2ProductName",
		MAX(a."total_ordered") as top_selling
FROM ("sales_report" a
	 JOIN "sales_by_sku" b
	 ON a."productsku" = b."productSKU")
JOIN "all_sessions" d
ON d."productSKU" = a."productsku"
GROUP BY "country",
		"city",
		"productsku",
		"v2ProductName",
		a."total_ordered"
ORDER BY 
		a."total_ordered" DESC,
		"country" DESC,
		"city"
```
For the query above, it contains undesired values such as "not available in demo dataset" or "(not set)". We need to apply WHERE to exclude those values 
```SQL
SELECT "productsku",
		"country",
		"city",
		"v2ProductName",
		MAX(a."total_ordered") as top_selling
FROM ("sales_report" a
	 JOIN "sales_by_sku" b
	 ON a."productsku" = b."productSKU")
JOIN "all_sessions" d
ON d."productSKU" = a."productsku"
WHERE "city" <> 'not available in demo dataset'AND
		"city" <> '(not set)'
GROUP BY "country",
		"city",
		"productsku",
		"v2ProductName",
		a."total_ordered"
ORDER BY 
		a."total_ordered" DESC,
		"country" DESC,
		"city"
```