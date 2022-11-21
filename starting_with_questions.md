Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```SQL
SELECT "country",
		"city",
		MAX("totalTransactionRevenue") AS revenue
FROM all_sessions
GROUP BY "totalTransactionRevenue",
		"country",
		"city"
HAVING MAX("totalTransactionRevenue") IS NOT NULL		
ORDER BY revenue DESC
LIMIT 20;

```
Answer:
```SQL
"country"	"city"	"revenue"
"United States"	"not available in demo dataset"	1015480000
"United States"	"not available in demo dataset"	1005500000
"United States"	"not available in demo dataset"	849700000
"United States"	"not available in demo dataset"	747000000
"United States"	"Atlanta"	742480000
"United States"	"Sunnyvale"	649240000
"Israel"	"Tel Aviv-Yafo"	602000000
"United States"	"not available in demo dataset"	396000000
"United States"	"not available in demo dataset"	391000000
"United States"	"Los Angeles"	363000000
"United States"	"Seattle"	358000000
"Australia"	"Sydney"	358000000
"United States"	"not available in demo dataset"	324000000
"United States"	"Chicago"	306000000
"United States"	"Palo Alto"	305000000
"United States"	"San Francisco"	301000000
"United States"	"not available in demo dataset"	241000000
"United States"	"San Francisco"	202000000
"United States"	"Sunnyvale"	200000000
"United States"	"San Francisco"	196090000
```

**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT DISTINCT a."city",
				a."country",
				ROUND(AVG(b."orderedQuantity"),3)
FROM "products" b
JOIN "all_sessions" a
ON a."productSKU" = b."SKU" IS NOT NULL
GROUP BY  "city",
			"country"			
ORDER BY "city" DESC,
		"country"
LIMIT 20;
```
Answer:
```SQL
"city"	"country"	"round"
"Zurich"	"Switzerland"	143.266
"Zhongli District"	"Taiwan"	143.266
"Zagreb"	"Croatia"	143.266
"Yokohama"	"Japan"	143.266
"Yokohama"	"United States"	143.266
"Wroclaw"	"Poland"	143.266
"Wrexham"	"United Kingdom"	143.266
"Westville"	"South Africa"	143.266
"Westlake Village"	"United States"	143.266
"Wellesley"	"United States"	143.266
"Watford"	"United Kingdom"	143.266
"Waterloo"	"Canada"	143.266
"Washington"	"United States"	143.266
"Warsaw"	"Poland"	143.266
"Vladivostok"	"Russia"	143.266
"Vilnius"	"Lithuania"	143.266
"Villeneuve-d'Ascq"	"France"	143.266
"Vienna"	"Austria"	143.266
"Vancouver"	"Canada"	143.266
"Vancouver"	"United States"	143.266
```

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT DISTINCT "country",
				"city",
				"v2ProductCategory",
				a."total_ordered"
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
		"v2ProductCategory",
		a."total_ordered"
ORDER BY 
		"total_ordered" DESC,
		"country" DESC,
		"city"
LIMIT 20;
```
Answer: 

There is an increase demand in Home Office and Writing Instruments in United State and in most of the cities 
```SQL
"country"	"city"	"v2ProductCategory"	"total_ordered"
"United States"	"Boston"	"Home/Office/"	456
"United States"	"Los Angeles"	"Home/Office/"	456
"United States"	"Mountain View"	"Home/Office/Writing Instruments/"	456
"United States"	"Mountain View"	"Home/Office/"	456
"United States"	"New York"	"Home/Office/"	456
"United States"	"Palo Alto"	"Home/Office/"	456
"United States"	"San Francisco"	"Home/Office/"	456
"United States"	"San Jose"	"Home/Office/"	456
"United States"	"Santa Clara"	"Home/Office/Writing Instruments/"	456
"United States"	"Seattle"	"Home/Office/"	456
"United States"	"Sunnyvale"	"Home/Office/"	456
"South Korea"	"Seoul"	"Home/Office/Writing Instruments/"	456
"India"	"Pune"	"Home/Office/"	456
"United States"	"Chicago"	"Home/Drinkware/Water Bottles and Tumblers/"	334
"United States"	"Chicago"	"Home/Drinkware/"	334
"United States"	"Los Angeles"	"Home/Drinkware/"	334
"United States"	"Los Angeles"	"Home/Drinkware/Water Bottles and Tumblers/"	334
"United States"	"Mountain View"	"Home/Shop by Brand/Google/"	334
"United States"	"New York"	"Home/Shop by Brand/Google/"	334
"United States"	"Palo Alto"	"Home/Shop by Brand/Google/"	334
```

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
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
LIMIT 10;
```

Answer: TOP 10 selling products
1. "Ballpoint LED Light Pen"
2. "Google 17oz Stainless Steel Sport Bottle"
3. "Leatherette Journal"
4. "Google Spiral Journal with Pen"
5. "Koozie Can Kooler"
6. "Foam Can and Bottle Cooler"
7. "Google Blackout Cap"
8. "Android 17oz Stainless Steel Sport Bottle"
9. "Google Sunglasses"
10. "Nest® Cam Outdoor Security Camera - USA"

As we can see in the data, there is a positive correlation from others countries to United State with top selling product
```SQL
"productsku"	"country"	"city"	"v2ProductName"	"top_selling"
"GGOEGOAQ012899"	"United States"	"Boston"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Los Angeles"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Mountain View"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"New York"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Palo Alto"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"San Francisco"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"San Jose"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Santa Clara"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Seattle"	"Ballpoint LED Light Pen"	456
"GGOEGOAQ012899"	"United States"	"Sunnyvale"	"Ballpoint LED Light Pen"	456
```





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```SQL
SELECT "productsku",
		"country",
		"city",
		ROUND(SUM(("productPrice")/1000000*a."total_ordered"),2) as revenue
FROM ("sales_report" a
	 JOIN "sales_by_sku" b
	 ON a."productsku" = b."productSKU")
JOIN "all_sessions" d
ON d."productSKU" = a."productsku"
WHERE "city" <> 'not available in demo dataset'AND
		"city" <> '(not set)'
GROUP BY "country",
		"city",
		"productsku"
ORDER BY 
		"revenue" DESC,
		"country",
		"city"
LIMIT 20;


Answer: 

United State is not only the biggest market which brings highest revenue in North America but also the biggest market in the world. The second country can be considered as having high revenue is UK. On the other hand, ASIA market and EU market are still low due to purchasing in-store products habit. However, ecommerce is currently increasing drastically  in ASIA countries, as a result, I believe that these countries will be shifted purchasing behavior in near future.

```SQL
"productsku"	"country"	"city"	"revenue"
"GGOENEBB078899"	"United States"	"Mountain View"	421872.00
"GGOENEBQ078999"	"United States"	"Mountain View"	294112.00
"GGOENEBJ079499"	"United States"	"Mountain View"	266678.00
"GGOENEBQ078999"	"United States"	"Palo Alto"	138096.00
"GGOENEBJ079499"	"United States"	"San Francisco"	121636.00
"GGOENEBQ078999"	"United States"	"Sunnyvale"	115808.00
"GGOENEBQ078999"	"United States"	"New York"	102480.00
"GGOENEBQ078999"	"United States"	"San Francisco"	97888.00
"GGOENEBJ079499"	"United States"	"Chicago"	88830.00
"GGOENEBB078899"	"United States"	"Sunnyvale"	85170.00
"GGOENEBJ079499"	"United States"	"Sunnyvale"	84224.00
"GGOENEBJ079499"	"United States"	"Palo Alto"	74824.00
"GGOENEBB078899"	"United States"	"Palo Alto"	73032.00
"GGOENEBJ079499"	"United Kingdom"	"London"	70218.00
"GGOENEBB078899"	"United States"	"San Francisco"	60894.00
"GGOENEBQ079199"	"United States"	"Mountain View"	58212.00
"GGOENEBB078899"	"United States"	"New York"	52734.00
"GGOEGDHC074099"	"United States"	"Mountain View"	50741.28
"GGOEGDHC074099"	"United States"	"Los Angeles"	48202.88
"GGOENEBJ079499"	"United States"	"New York"	46812.00
```

