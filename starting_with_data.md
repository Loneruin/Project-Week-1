Question 1: top products has the highest number of visit and pageviews before customer decide to purchase. Are there any correlation between number of visit and pageviews

SQL Queries:

```SQL
SELECT DISTINCT a."fullVisitorId",
				a."v2ProductCategory",
				a."v2ProductName",
				b."visitnumber",
				b."pageviews"
FROM all_sessions a
JOIN analytics b
ON	a."fullVisitorId" = b."fullvisitorid"
WHERE "v2ProductCategory" <> '${escCatTitle}' AND
	 "v2ProductCategory" <> '(not set)'
GROUP BY "fullVisitorId",
		"v2ProductCategory",
		a."v2ProductName",
		"visitnumber",
		b."pageviews"
ORDER BY "visitnumber" DESC,
		b."pageviews"
```
Answer: 
Top 10 visit number products:
1. "Crunch Noise Dog Toy"
2. "Android 24 oz Contigo Bottle"
3. "Android BTTF Moonshot Graphic Tee"
4. "23 oz Wide Mouth Sport Bottle"
5. "Google 17oz Stainless Steel Sport Bottle"
6. "20 oz Stainless Steel Insulated Tumbler"
7. "Google Bib White"
8. "YouTube Custom Decals"
9. "Straw Beach Mat"
10. "Android Wool Heather Cap Heather/Black"

There is no correlation between number of visit and pageviews when purchasing a product
```SQL
"fullVisitorId"	"v2ProductCategory"	"v2ProductName"	"visitnumber"	"pageviews"
"0824839726118485274"	"Home/Accessories/"	"Crunch Noise Dog Toy"	394	4
"0824839726118485274"	"Home/Accessories/Drinkware/"	"Android 24 oz Contigo Bottle"	394	4
"0824839726118485274"	"Home/Apparel/Men's/Men's-T-Shirts/"	"Android BTTF Moonshot Graphic Tee"	394	4
"0824839726118485274"	"Home/Drinkware/"	"23 oz Wide Mouth Sport Bottle"	394	4
"0824839726118485274"	"Home/Drinkware/"	"Google 17oz Stainless Steel Sport Bottle"	394	4
"0824839726118485274"	"Home/Drinkware/Water Bottles and Tumblers/"	"20 oz Stainless Steel Insulated Tumbler"	394	4
"0824839726118485274"	"Home/Shop by Brand/Google/"	"Google Bib White"	394	4
"0824839726118485274"	"Home/Shop by Brand/YouTube/"	"YouTube Custom Decals"	394	4
"0824839726118485274"	"Home/Accessories/"	"Crunch Noise Dog Toy"	393	3
"0824839726118485274"	"Home/Accessories/Drinkware/"	"Android 24 oz Contigo Bottle"	393	3
```



Question 2: Which products which have sentiment score less than 0

SQL Queries:
```SQL
ELECT DISTINCT a."SKU",
		a."name",
		b."v2ProductCategory",
		("sentimentScore"),
		"sentimentMagnitude"
FROM products a
JOIN all_sessions b
ON a."SKU" = b."productSKU"
WHERE "v2ProductCategory" <> '(not set)' AND
	 "v2ProductCategory" <> '${escCatTitle}' AND
	 "sentimentScore" < 0
GROUP BY a."name",
		a."SKU",
		"sentimentScore",
		"sentimentMagnitude",
		b."v2ProductCategory"
ORDER BY "sentimentScore",
		"sentimentMagnitude"
LIMIT 10;

```

Answer: 
There are 127 products which have sentiment scores less than 0. Moreover, 7 dog Frisbee which has -0.6, is considered the least one. The customer sentiment score is computed by weighing each found sentiment event (positive or negative), with greater significance placed on the sentiment events that occurred toward the end of the interaction.  In essence, the customer sentiment score answers the question, did the customer leave the interaction happy, or dissatisfied? As a consequence, we need to take a look at the quality of the low sentiment score products and find the reason why customers are unhappy with those products
```SQL
"SKU"	"name"	"v2ProductCategory"	"sentimentScore"	"sentimentMagnitude"
"GGOEGAAX0098"	"7 Dog Frisbee"	"Home/Accessories/Pet/"	-0.6	0.2
"GGOEGAAX0098"	"7 Dog Frisbee"	"Home/Lifestyle/"	-0.6	0.2
"GGOEGAAX0595"	" Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	"Home/Apparel/Men's/Men's-Outerwear/"	-0.5	0.6
"GGOEGAAX0344"	" Women's Vintage Hero Tee Platinum"	"Home/Apparel/Women's/"	-0.5	1.1
"GGOEGAAX0344"	" Women's Vintage Hero Tee Platinum"	"Home/Shop by Brand/"	-0.5	1.1
"GGOEGAAX0344"	" Women's Vintage Hero Tee Platinum"	"Home/Shop by Brand/Google/"	-0.5	1.1
"GGOEGAAX0344"	" Women's Vintage Hero Tee Platinum"	"Home/Apparel/Women's/Women's-T-Shirts/"	-0.5	1.1
"GGOEGAAX0344"	" Women's Vintage Hero Tee Platinum"	"Home/Apparel/"	-0.5	1.1
"GGOEGAAX0351"	" Men's Vintage Henley"	"Home/Apparel/Men's/Men's-T-Shirts/"	-0.5	1.4
"GGOEGAAX0351"	" Men's Vintage Henley"	"Home/Brands/YouTube/"	-0.5	1.4
```


Question 3: what do we need to notice between total order of the product and restocking lead time

SQL Queries:
```SQL
SELECT "productsku",
		"v2ProductName",
		"v2ProductCategory",
		a."total_ordered",
		"stockLevel",
		"restockingLeadTime"
	FROM("sales_report" a
	 	JOIN "products" b
	 	ON a."productsku" = b."SKU")
JOIN all_sessions d
ON d."productSKU" = a."productsku"
WHERE "v2ProductCategory" <> '(not set)'AND
	"v2ProductCategory" <> '${escCatTitle}'
GROUP BY "productsku",
		"v2ProductName",
		"v2ProductCategory",
		"stockLevel",
		"restockingLeadTime",
		a."total_ordered"
ORDER BY "total_ordered" DESC,
		"restockingLeadTime"
LIMIT 10;
```
Answer: In the result, we see that for the most purchased item, we have an average amount of restocking lead time. In my point of view, we may stock more top-selling products and check in stock regularly to maintain the flow of selling products

```SQL
"productsku"	"v2ProductName"	"v2ProductCategory"	"total_ordered"	"stockLevel"	"restockingLeadTime"
"GGOEGOAQ012899"	"Ballpoint LED Light Pen"	"Home/Office/Writing Instruments/"	456	2098	11
"GGOEGOAQ012899"	"Ballpoint LED Light Pen"	"Home/Office/"	456	2098	11
"GGOEGDHC074099"	"Google 17oz Stainless Steel Sport Bottle"	"Home/Accessories/Drinkware/"	334	1390	13
"GGOEGDHC074099"	"Google 17oz Stainless Steel Sport Bottle"	"Home/Shop by Brand/Google/"	334	1390	13
"GGOEGDHC074099"	"Google 17oz Stainless Steel Sport Bottle"	"Home/Drinkware/Water Bottles and Tumblers/"	334	1390	13
"GGOEGDHC074099"	"Google 17oz Stainless Steel Sport Bottle"	"Home/Drinkware/"	334	1390	13
"GGOEGOCB017499"	"Leatherette Journal"	"Home/Office/Notebooks & Journals/"	319	4978	36
"GGOEGOCB017499"	"Leatherette Journal"	"Home/Office/"	319	4978	36
"GGOEGOCC077999"	"Google Spiral Journal with Pen"	"Home/Office/Notebooks & Journals/"	290	4668	10
"GGOEGFYQ016599"	"Foam Can and Bottle Cooler"	"Home/Lifestyle/Fun/"	253	4495	10
```

Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
