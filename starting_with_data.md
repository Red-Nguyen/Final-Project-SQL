# Question 1: Top 5 highest city views 
SQL Queries:
	SELECT city, sum(pageviews) as totalviews FROM all_sessions
	WHERE city IS NOT NULL
	GROUP BY city
	ORDER BY totalviews DESC
	LIMIT 5;

Answer: 
	| City          | Total Views |
	| ------------- | ----------- |
	| Mountain View | 6624        |
	| New York      | 3230        |
	| San Francisco | 2293        |
	| Sunnyvale     | 1982        |
	| San Jose      | 1267        |


# Question 2: Which months has the highest revenue and lowest revenue with SUM revenue

SQL Queries:
	(SELECT DISTINCT (EXTRACT( MONTH FROM date)) AS MONTH, SUM(revenue_in_million) AS SUM_revenue, 'highest' as level
	 FROM analytics 
	GROUP BY DISTINCT (EXTRACT( MONTH FROM date))
	ORDER BY SUM_revenue DESC
	LIMIT 1)
	UNION
	(SELECT DISTINCT (EXTRACT( MONTH FROM date))AS MONTH, SUM(revenue_in_million) AS SUM_revenue, 'lowest' as level
	 FROM analytics 
	GROUP BY DISTINCT (EXTRACT( MONTH FROM date))
	ORDER BY SUM_revenue 
	LIMIT 1)
Answer:  
	| Month | Sum Revenue | Level   |
	| ----- | ----------- | ------- |
	| 7     | 413331.85   | highest |
	| 8     | 19686.40    | lowest  |

# Question 3: Percent between visitor and their ordered

SQL Queries:
	SELECT ROUND((COUNT(CASE WHEN units_sold IS NOT NULL OR units_sold > 0 THEN 1 END) * 100.0 / COUNT(visitid)),2) AS "percent order",
	(MAX (date) - MIN(date)) as days
	FROM analytics;
Answer:
	| Percent Order | Days |
	| ------------- | ---  |
	| 2.21          | 92   |



