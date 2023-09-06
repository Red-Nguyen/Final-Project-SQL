# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals

	- Explore the data to determine what information the tables are telling me.
	- Understanding what is this, and what is the purpose of this data. These columns and tables are used for what.
	- Find the relationship between these columns and tables.
	- Data cleansing
	- Analyze data by answering questions
	- QA questions to ensure that the database system is reliable, performs well, and is consistent.

## Process

Step 1 - Download all the csv files and import in Postgresql in TYPE text to preserving data integrity

Step 2 - Set up data type for each columns

Step 3 - Explore these columns and tables for understanding, linked together, set up PRIMARY KEY as well

Step 4-  Cleaning and transforming the data

Step 5 - Diving into the data with query for questions and answers

Step 6 - Quality assurance


## Results
Question 1. Which cities and countries have the highest level of transaction revenues on the site?

Result:
		| City          | Country       | Total Revenue (Million) | Currency Code |
		| ------------- | ------------- | ----------------------- | ------------- |
		| San Francisco | United States | 1456.18                 | USD           |

Question 2. What is the average number of products ordered from visitors in each city and country?

Result: 
		| Country   | City           | Avg Product Ordered |
		|-----------|----------------|---------------------|
		| Argentina | Buenos Aires   | 434.5               |
		| Argentina | Rosario        | 433                 |
		| Argentina | Santa Fe       | 1932.5              |

Question 3. Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?

Result: Most products are sold in the US, Canada is a cold area so Outerwear is also sold, in Australia likes Nest-USA
		| Country       | Product Category                                 | Count |
		| ------------- | ------------------------------------------------ | ----- |
		| United States | Home/Nest/Nest-USA/                              | 17    |
		| United States | Nest-USA                                         | 8     |
		| United States | Apparel                                          | 6     |

Question 4. What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?

Result: 
		| Country        | City           | Product Name                                | Rank |
		| ---------------| ---------------| ------------------------------------------- | ---- |
		| United States  | Mountain View  | Nest® Cam Indoor Security Camera - USA      | 1    |
		| United States  | San Francisco  | Google Kick Ball                            | 1    |

Question 5. Can we summarize the impact of revenue generated from each city/country?

Result:
		| City          | Country       | Total Revenue (Million) | Currency Code |
		| ------------- | ------------- | ----------------------- | ------------- |
		| San Francisco | United States | 1456.18                 | USD           |
		| Sunnyvale     | United States | 992.23                  | USD           |

## Challenges 

	The data is not clear, some tables don't have PRIMARY KEY, duplicate information, and some values are not inconsistent.
	---> So, it's hard and maybe impossible to give the correct answer.

## Future Goals

	If I had more time on this data, I would spend more time on data cleaning as it is a crucial  step in data analysis.