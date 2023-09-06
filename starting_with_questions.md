Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
	SELECT city, country, SUM(totaltransactionrevenue_in_million) AS total_revenue_in_million, currencycode FROM all_sessions
	WHERE (totaltransactionrevenue_in_million IS NOT NULL AND timeonsite IS NOT NULL AND city IS NOT NULL )
	GROUP BY city, country,currencycode
	ORDER BY SUM(totaltransactionrevenue_in_million) DESC
	LIMIT 1;
Answer:
| City          | Country       | Total Revenue (Million) | Currency Code |
| ------------- | ------------- | ----------------------- | ------------- |
| San Francisco | United States | 1456.18                 | USD           |

**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
	SELECT a.country,a.city,ROUND(AVG(orderedquantity), 2) AS AVG_product_ordered   
	FROM all_sessions a    
	JOIN products p ON p.sku = a.productsku
	WHERE a.city IS NOT NULL AND a.country IS NOT NULL
	GROUP BY   a.city,  a.country
	HAVING ROUND(AVG(orderedquantity), 2) >0
	ORDER BY  a.country,a.city;  
	
Answer:  268 Rows


| Country   | City           | Avg Product Ordered |
|-----------|----------------|---------------------|
| Argentina | Buenos Aires   | 434.5               |
| Argentina | Rosario        | 433                 |
| Argentina | Santa Fe       | 1932.5              |
| Australia | Adelaide       | 54                  |
| Australia | Brisbane       | 274.5               |
| Australia | Los Angeles    | 917                 |
| Australia | Melbourne      | 659.38              |
| Australia | Mountain View  | 33                  |
| Australia | Perth          | 1035.8              |
| Australia | Sydney         | 396.09              |



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SQL Queries:
	SELECT country, v2productcategory, COUNT(country) FROM all_sessions
	WHERE totaltransactionrevenue_in_million  IS NOT NULL
	GROUP BY v2productcategory, country
	ORDER BY count DESC, country;

Answer: Most products are sold in the US, Canada is a cold area so Outerwear is also sold, in Australia likes Nest-USA
| Country       | Product Category                                 | Count |
| ------------- | ------------------------------------------------ | ----- |
| United States | Home/Nest/Nest-USA/                              | 17    |
| United States | Nest-USA                                         | 8     |
| United States | Apparel                                          | 6     |
| United States | Home/Apparel/Men's/Men's-T-Shirts/               | 5     |
| United States | Home/Apparel/Women's/Women's-T-Shirts/           | 3     |
| United States | Home/Apparel/Kid's/Kid's-Infant/                 | 3     |
| United States | ${escCatTitle}                                   | 2     |
| United States | Bags                                             | 2     |
| United States | Electronics                                      | 2     |
| United States | Home/Accessories/Fun/                            | 2     |
| United States | Home/Bags/                                       | 2     |
| United States | Home/Drinkware/                                  | 2     |
| United States | Home/Shop by Brand/Google/                       | 2     |
| United States | Lifestyle                                        | 2     |
| United States | Waze                                             | 2     |
| Australia     | Nest-USA                                         | 1     |
| Canada        | Home/Apparel/Men's/Men's-Outerwear/              | 1     |
| Canada        | Apparel                                          | 1     |
| Israel        | Home/Nest/Nest-USA/                              | 1     |
| Switzerland   | Home/Apparel/Men's/Men's-T-Shirts/               | 1     |


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries: What is the top-selling product from each city/country?

WITH rankproduct AS (SELECT a.country,a.city, v2productname,
	ROUND(SUM(orderedquantity), 2) AS AVG_product_ordered, 
	RANK() OVER( PARTITION BY a.country, a.city ORDER BY ROUND(SUM(orderedquantity), 2 )DESC) AS Rank 
	FROM all_sessions a    
	JOIN products p ON p.sku = a.productsku
	WHERE a.city IS NOT NULL AND a.country IS NOT NULL
	GROUP BY   a.city,  a.country, v2productname
	HAVING ROUND(SUM(orderedquantity), 2) >0 )
SELECT country,city,v2productname,rank FROM rankproduct
	WHERE rank = 1
	ORDER BY avg_product_ordered DESC
	
Answer: 257 rows

| Country        | City           | Product Name                                | Rank |
| ---------------| ---------------| ------------------------------------------- | ---- |
| United States  | Mountain View  | Nest® Cam Indoor Security Camera - USA      | 1    |
| United States  | San Francisco  | Google Kick Ball                            | 1    |
| United States  | New York       | Google Kick Ball                            | 1    |
| United States  | Chicago        | Google Kick Ball                            | 1    |
| United States  | Palo Alto      | Nest® Cam Outdoor Security Camera - USA     | 1    |
| United States  | Sunnyvale      | Nest® Cam Outdoor Security Camera - USA     | 1    |
| United States  | San Bruno      | Google Kick Ball                            | 1    |
| United States  | Kirkland       | Google Kick Ball                            | 1    |
| Ireland        | Dublin         | Google Kick Ball                            | 1    |
| United States  | San Diego      | Google Kick Ball                            | 1    |
| United States  | Los Angeles    | Google Kick Ball                            | 1    |
| Russia         | Moscow         | Google Kick Ball                            | 1    |


SQL Queries: Can we find any pattern worthy of noting in the products sold?**

WITH rankproduct AS (SELECT a.country,a.city, v2productname,
	ROUND(SUM(orderedquantity), 2) AS AVG_product_ordered, 
	RANK() OVER( PARTITION BY a.country, a.city ORDER BY ROUND(SUM(orderedquantity), 2 )DESC) AS Rank 
	FROM all_sessions a    
	JOIN products p ON p.sku = a.productsku
	WHERE a.city IS NOT NULL AND a.country IS NOT NULL
	GROUP BY   a.city,  a.country, v2productname
	HAVING ROUND(SUM(orderedquantity), 2) >0 )
SELECT 	v2productname, country, COUNT(*) FROM  rankproduct
WHERE rank = 1
	GROUP BY v2productname, country
	ORDER BY COUNT(*) DESC
	LIMIT 5	
	
Answer: 
| v2productname                               | Country        | Count |
| ------------------------------------------ | ---------------| ------|
| Google Kick Ball                            | United States  | 9     |
| Nest® Cam Indoor Security Camera - USA      | United States  | 6     |
| YouTube Custom Decals                       | India          | 5     |
| Google 22 oz Water Bottle                   | United States  | 4     |
| YouTube Custom Decals                       | United States  | 4     |

The most frequently sold products are "Google Kick Ball" and "Nest® Cam Indoor Security Camera - USA".

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT city, country, SUM(totaltransactionrevenue_in_million) AS total_revenue_in_million, currencycode FROM all_sessions
	WHERE (totaltransactionrevenue_in_million IS NOT NULL AND timeonsite IS NOT NULL AND city IS NOT NULL )
	GROUP BY city, country,currencycode
	ORDER BY SUM(totaltransactionrevenue_in_million) DESC;

Answer:

| City          | Country       | Total Revenue (Million) | Currency Code |
| ------------- | ------------- | ----------------------- | ------------- |
| San Francisco | United States | 1456.18                 | USD           |
| Sunnyvale     | United States | 992.23                  | USD           |
| Atlanta       | United States | 854.44                  | USD           |
| Palo Alto     | United States | 608.00                  | USD           |
| Tel Aviv-Yafo | Israel        | 602.00                  | USD           |
| Los Angeles   | United States | 479.48                  | USD           |
| Chicago       | United States | 449.52                  | USD           |
| New York      | United States | 403.36                  | USD           |





