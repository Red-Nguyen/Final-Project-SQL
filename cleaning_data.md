What issues will you address by cleaning the data?
	Convert the data type of all columns in all tables.
	Some original column names have capital letters so it will be sensitive when we are coding and I change to uppercase to lowercase.
	Remove the leading space name column
	SET PRIMARY KEY for a table where it has PRIMARY KEY
	DROP NULL column
	ROUND unit price/1000000
	Identifying data inconsistencies or errors in your dataset at analytics table such as find duplicate column and DROP it, etc.	
Queries:
	STEP 1  ALTER TYPE,  DROP NULL column, make column insensitive, find duplicate and set PRIMARY KEY
	STEP 2 Round unit price/1000000 and Identifying data inconsistencies or errors in your dataset at analytics table

# --- STEP 1  ALTER TYPE,  DROP NULL column, make column insensitive, find duplicate and set PRIMARY KEY

# -- ALTER TYPE of sales_by_sku table
		ALTER TABLE sales_by_sku
		ALTER COLUMN total_ordered TYPE INT USING total_ordered::INT,
		ALTER COLUMN productsku TYPE VARCHAR (50) USING productsku::VARCHAR (50);
# --CHECK unique sales_by_sku to define the column as a primary key
		SELECT productsku, count(*) FROM sales_by_sku
		GROUP BY productsku
		HAVING count(*) >1
# -- Define the column as a primary key
		ALTER TABLE sales_by_sku
		ADD PRIMARY KEY (productsku);
# -- ALTER TYPE of sales_report
		ALTER TABLE sales_report
		ALTER COLUMN productsku TYPE VARCHAR (50) USING productsku::VARCHAR (50),
		ALTER COLUMN total_ordered TYPE INT USING total_ordered::INT,
		ALTER COLUMN name TYPE VARCHAR (100) USING name::VARCHAR (100),
		ALTER COLUMN stocklevel TYPE INT USING stocklevel::INT,
		ALTER COLUMN sentimentkagnitude TYPE NUMERIC USING sentimentkagnitude::NUMERIC,
		ALTER COLUMN sentimentscore TYPE Double Precision USING sentimentscore::Double Precision,
		ALTER COLUMN ratio TYPE Double Precision USING ratio::Double Precision;
# --Rename sentimentmagnitude column
		ALTER TABLE sales_report
		RENAME COLUMN sentimentkagnitude TO sentimentmagnitude;
# --Remove leading space at "name" column
		UPDATE sales_report
		SET name = TRIM(LEADING ' ' FROM name)
		WHERE name LIKE ' %';
# --CHECK unique productsku to define the column as a primary key
		SELECT productsku, count(*) FROM sales_report
		GROUP BY productsku
		HAVING count(*) >1
# -- Define the column as a primary key
		ALTER TABLE sales_report
		ADD PRIMARY KEY (productsku);
# -- ALTER TYPE of products table
		ALTER TABLE products
		ALTER COLUMN sku TYPE VARCHAR (50) USING sku::VARCHAR (50),
		ALTER COLUMN name TYPE VARCHAR (100) USING name::VARCHAR (100),
		ALTER COLUMN orderedquantity TYPE INT USING orderedquantity::INT,
		ALTER COLUMN stocklevel TYPE INT USING stocklevel::INT,
		ALTER COLUMN restockingleadtime TYPE NUMERIC USING restockingleadtime::NUMERIC,
		ALTER COLUMN sentimentscore TYPE Double Precision USING sentimentscore::Double Precision,
		ALTER COLUMN sentimentmagnitude TYPE Double Precision USING sentimentmagnitude::Double Precision;
# --Remove leading space at "name" column
		UPDATE products
		SET name = TRIM(LEADING ' ' FROM name)
		WHERE name LIKE ' %';
# --CHECK unique sku to define the column as a primary key
		SELECT sku, count(*) FROM products
		GROUP BY sku
		HAVING count(*) >1
# --Define the column as a primary key
		ALTER TABLE products
		ADD PRIMARY KEY (sku);
# --ALTER TYPE of analytics table
		ALTER TABLE analytics
		ALTER COLUMN visitnumber TYPE INT USING visitnumber::INT,
		ALTER COLUMN visitid TYPE INT USING visitid::INT,
		ALTER COLUMN visitstarttime TYPE DOUBLE PRECISION USING visitid::DOUBLE PRECISION,
		ALTER COLUMN date TYPE date USING date::date,
		ALTER COLUMN fullvisitorid TYPE numeric USING fullvisitorid::numeric,
		ALTER COLUMN channelgrouping TYPE VARCHAR (50) USING channelgrouping::VARCHAR (50),
		ALTER COLUMN socialengagementtype TYPE VARCHAR (50) USING socialengagementtype::VARCHAR (50),
		ALTER COLUMN units_sold TYPE INT USING units_sold::INT,
		ALTER COLUMN pageviews TYPE INT USING pageviews::INT,
		ALTER COLUMN timeonsite TYPE NUMERIC USING timeonsite::NUMERIC,
		ALTER COLUMN bounces TYPE INT USING bounces::INT,
		ALTER COLUMN revenue TYPE NUMERIC USING revenue ::NUMERIC,
		ALTER COLUMN unit_price TYPE NUMERIC USING unit_price ::NUMERIC;
# --Check column is FULL
		SELECT * FROM analytics
		WHERE userid IS NOT NULL;
# --DROP COLUMN is FULL
		ALTER TABLE analytics
		DROP COLUMN userid;
# --ALTER TYPE of all_sessions table
		ALTER TABLE all_sessions
		ALTER COLUMN fullvisitorid TYPE numeric USING fullvisitorid::numeric,
		ALTER COLUMN channelgrouping TYPE VARCHAR (50) USING channelgrouping::VARCHAR (50),
		ALTER COLUMN time TYPE INTERVAL USING time::INTERVAL,
		ALTER COLUMN country TYPE VARCHAR (50) USING country::VARCHAR (50),
		ALTER COLUMN city TYPE VARCHAR (50) USING city::VARCHAR (50),
		ALTER COLUMN totaltransactionrevenue TYPE NUMERIC USING totaltransactionrevenue::NUMERIC,
		ALTER COLUMN transactions TYPE INT USING transactions::INT,
		ALTER COLUMN timeonsite TYPE NUMERIC USING timeonsite::NUMERIC,
		ALTER COLUMN pageviews TYPE INT USING pageviews::INT,
		ALTER COLUMN sessionqualitydim TYPE INT USING sessionqualitydim::INT,
		ALTER COLUMN date TYPE date USING date::date,
		ALTER COLUMN visitid TYPE INT USING visitid::INT,
		ALTER COLUMN TYPE TYPE VARCHAR (50) USING TYPE::VARCHAR (50),
		ALTER COLUMN productquantity TYPE NUMERIC USING productquantity::NUMERIC,
		ALTER COLUMN productprice TYPE NUMERIC USING productprice::NUMERIC,
		ALTER COLUMN productrevenue TYPE NUMERIC USING productrevenue::NUMERIC,
		ALTER COLUMN productsku TYPE VARCHAR (50) USING productsku::VARCHAR (50),
		ALTER COLUMN v2productname TYPE VARCHAR (100) USING v2productname::VARCHAR (100),
		ALTER COLUMN v2productcategory TYPE VARCHAR (100) USING v2productcategory::VARCHAR (100),
		ALTER COLUMN productvariant TYPE VARCHAR (50) USING productvariant::VARCHAR (50),
		ALTER COLUMN currencycode TYPE VARCHAR (50) USING currencycode::VARCHAR (50),
		ALTER COLUMN transactionrevenue TYPE NUMERIC USING transactionrevenue::NUMERIC,
		ALTER COLUMN transactionid TYPE VARCHAR (50) USING transactionid::VARCHAR (50),
		ALTER COLUMN pagetitle TYPE VARCHAR (50) USING pagetitle::VARCHAR (50),
		ALTER COLUMN pagepathlevel1 TYPE VARCHAR (50) USING pagepathlevel1::VARCHAR (50),
		ALTER COLUMN ecommerceaction_type TYPE INT USING ecommerceaction_type::INT,
		ALTER COLUMN ecommerceaction_step TYPE INT USING ecommerceaction_step::INT,
		ALTER COLUMN ecommerceaction_option TYPE VARCHAR (50) USING ecommerceaction_option :: VARCHAR (50);
# --DROP COLUMN is FULL
		ALTER TABLE all_sessions
		DROP COLUMN productrefundamount;
		ALTER TABLE all_sessions
		DROP COLUMN itemquantity;
		ALTER TABLE all_sessions
		DROP COLUMN itemrevenue;
		ALTER TABLE all_sessions
		DROP COLUMN searchkeyword;   
# -- Change city without value to NULL
		UPDATE all_sessions
		SET city = NULL
		WHERE city = 'not set' OR city ='(not set)' OR city = 'not available in demo dataset';

# --- STEP 2 Round unit price/1000000 and Identifying data inconsistencies or errors in your dataset at analytics table
# --ROUND unit price/1000000
		UPDATE all_sessions
		SET productprice = ROUND(productprice / 1000000, 2);
		ALTER TABLE all_sessions
		RENAME COLUMN productprice TO productprice_in_million;
		UPDATE all_sessions
		SET productrevenue = ROUND(productrevenue / 1000000, 2);
		ALTER TABLE all_sessions
		RENAME COLUMN productrevenue TO productrevenue_in_million;
		UPDATE all_sessions
		SET totaltransactionrevenue = ROUND(totaltransactionrevenue / 1000000, 2);
		ALTER TABLE all_sessions
		RENAME COLUMN totaltransactionrevenue TO totaltransactionrevenue_in_million;
		UPDATE analytics
		SET unit_price = ROUND(unit_price / 1000000, 2);
		ALTER TABLE all_sessions
		RENAME COLUMN unit_price TO unit_price_in_million;
		UPDATE analytics
		SET revenue = ROUND(revenue / 1000000, 2);
		ALTER TABLE analytics
		RENAME COLUMN revenue TO revenue_in_million;
# -- Identifying data inconsistencies or errors in your dataset at analytics table

	# 'Dupicate column visitid and visitstarttime, So I will DROP column visitstarttime'
		SELECT (visitid - visitstarttime)as diff FROM analytics
		WHERE (visitid - visitstarttime) <>0;
	# 'DROP column visitstarttime '
		ALTER TABLE analytics
		DROP COLUMN visitstarttime;
	# 'revenue >= unitprice - no confict'
		SELECT *  FROM analytics
		WHERE revenue > unit_price;
	# 'verify units_sold and revenue - no confict'
		SELECT * FROM analytics
		WHERE units_sold IS NULL AND revenue IS NOT NULL;
# --Identifying data inconsistencies or errors in your dataset at all_sessions table

	# 'verify time and timeonsite - no confict'
		SELECT * FROM all_sessions
		WHERE time IS NULL AND timeonsite IS NOT NULL;
	# 'productrevenue > productprice - no confict'
		SELECT * FROM all_sessions
		WHERE productprice  > productrevenue
	# 'Check restokingleadtime between products sku and sales_report sku and - no confict'
		SELECT sku, productsku, p.restockingleadtime, sr.restockingleadtime, 
		(p.restockingleadtime-sr.restockingleadtime) as diff_restocking
		FROM products p
		JOIN sales_report sr ON sr.productsku=p.sku
		WHERE (p.restockingleadtime-sr.restockingleadtime) <>0;