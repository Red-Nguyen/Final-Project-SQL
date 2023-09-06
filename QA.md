What are your risk areas? Identify and describe them.

QA Process:
Describe your QA process and include the SQL queries used to execute it.

# 1- Inconsistent - Check relationship between analytics and all_sessions via visitid - it does not clear. 
Same visitid --> sane channelgrouping but different fullvisitorid'
QUERY:

	SELECT an.fullvisitorid,s.fullvisitorid, an.channelgrouping,s.channelgrouping FROM analytics an
	JOIN all_sessions s ON an.visitid=s.visitid.
	LIMIT 3
Result:
	| FullVisitorID         | FullVisitorID-2       | ChannelGrouping   | ChannelGrouping-2    |
	| --------------------- | ----------------------| ----------------- | ---------------------|
	| 5651390349629647747   | 5651390000000000000   | Direct            | Direct               |
	| 3100863958861089834   | 3100860000000000000   | Paid Search       | Paid Search          |
	| 4215347458239853405   | 4215350000000000000   | Organic Search    | Organic Search       |

# 2 - Inconsistent - Compare ordered - there is difference ordered between products and sales report
QUERY:
	SELECT sku, productsku, orderedquantity, total_ordered, (p.orderedquantity-sr.total_ordered) as diff_ordered 
	FROM products p
	JOIN sales_report sr ON sr.productsku=p.sku;
Result: 454 rows

	| SKU            | Product SKU      | Ordered Quantity | Total Ordered  | Diff Ordered |
	| ---------------| -----------------| -----------------| ---------------| -------------|
	| GGOEGDWR015799 | GGOEGDWR015799   | 1058             | 5              | 1053         |
	| GGOEGAAX0081   | GGOEGAAX0081     | 545              | 42             | 503          |
	| GGOEAHPB076114 | GGOEAHPB076114   | 6                | 1              | 5            |
	| 9180850        | 9180850          | 0                | 0              | 0            |
	| GGOEYDHJ056099 | GGOEYDHJ056099   | 1465             | 50             | 1415         |
	
# 3 - Inconsistent -Different duration days between all_sessions and analytics
QUERY:
	SELECT 'days_of_analytics' AS table, (MAX (date) - MIN(date)) as days FROM analytics
	UNION
	SELECT 'days_of_all_sessions' AS table, (MAX (date) - MIN(date)) as days FROM all_sessions;
Result:
	| Table                   | Days |
	| ----------------------- | ---- |
	| days_of_all_sessions    | 365  |
	| days_of_analytics       | 92   |

# 4 - Duplicate visitid 
QUERY:
	SELECT visitid, COUNT(*) AS visit_count
	FROM all_sessions
	GROUP BY visitid
	HAVING COUNT(*)>1;
Result: 
	| VisitID      | Visit_Count |
	| ------------ | ----------- |
	| 1491424686   | 2           |
	| 1497986216   | 2           |
	| 1486495918   | 2           |
	| 1473382271   | 3           |
	
# 5 - Duplicate fullvisitorid
QUERY:	
	SELECT fullvisitorid, COUNT(*) AS fullvisitorid_count
	FROM all_sessions
	GROUP BY fullvisitorid
	HAVING COUNT(*)>1;
Result:
	| FullVisitorID       | FullVisitorID_Count  |
	| ------------------- | -------------------- |
	| 3651240000000000000 | 4                    |
	| 4278370000000000000 | 4                    |
	| 1129260000000000000 | 2                    |
	| 8045320000000000000 | 2                    |