# Customer Behavior Analysis- Dany's Diner

### Table of Contents
- [ Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Extraction/Preparation](#data-extraction/preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#results-findings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
-  [References](#references)
## Project Overview
This project is a case study about Dany’s daily food, Dany loves Nigerian food and decided to open a restaurant at the beginning of 2021, he sells his favourite foods which are
- Curry
- Sushi
- Ramen
Dany wants to understand his customers so as to deliver personalised experience and improve customer loyalty. He also want to decide whether to expand the existing customer loyalty program. lastly, wants some basic datasets which he and his team can easily inspect the data without needing to use SQL.

This Project aims to provide insights into customer behavior of a restaurant. By analysing the sales and menu data we seek to identify trends, make data driven recommendation and gain deeper understanding of Customer behavior.

### Data Sources
##### Sales, Menu and Member's data:
the dataset used for this analysis is from Dany's daily activity register, which contain detailed information about each customer and sales made by the restaurant. I created a database called Dany's_diner with the Menu, Members, and Sales table in it

### Tools
- SQL Server - for Data Analysis, Exploration and Extraction

### Data Extraction/Preparation
In the data preparation phase, we performed the following tasks:
1. Database creation
2. Inserted Sales, Menu and Member table
3. Data Analysis
4. creation of new column

### Exploratory Data Analysis
This involves exploring Sales, Menu and Member's data to answer key questions, such as:
- What is the total amount each customer spent at the restaurant?
- How many days has each customer visited the restaurant?
- What was the first item from the menu purchased by each customer?
- Which item was purchased just before the customer became a member?

### Data Analysis
- CTE
```sql
WITH last_purchase_before_membership AS	
	(SELECT s.customer_id, MAX(s.order_datE) AS last_purchase_date
	FROM menu m
	Join sales s ON s.product_id = m.product_id
	join members mb on s.customer_id=mb.customer_id
	where s.order_date < mb.join_date
	GROUP BY s.customer_id
)
SELECT lpbm.customer_id, m.product_name
FROM last_purchase_before_membership lpbm
JOIN sales s ON s.customer_id = lpbm.customer_id AND s.order_date = lpbm.last_purchase_date
JOIN menu m ON  s.product_id=m.product_id;
```
- Dense rank()
  ``` sql WITH product_popularity AS (SELECT m.product_name, s.customer_id, COUNT(*) AS purchase_count, 
	DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS rank
	FROM menu m
	JOIN sales s
	ON s.product_id = m.product_id
	GROUP BY s.customer_id, m.product_name)
SELECT pp.customer_id, pp.product_name, pp.purchase_count
FROM product_popularity pp
WHERE pp.rank = 1


- Left Join and Rank()

```sql WITH customer_data AS (SELECT s.customer_id, s.order_date, m.product_name, m.price, 
		CASE
			WHEN s.order_date >= mb.join_date
			THEN 'Y' ELSE 'N' END AS member
		FROM sales s
		Join menu m ON s.product_id = m.product_id
		left join members mb ON s.customer_id=mb.customer_id
		)
SELECT *,
CASE 
WHEN member = 'N' THEN NULL
ELSE RANK() OVER(PARTITION BY customer_id, member ORDER BY order_date) END AS ranking
From customer_data
ORDER BY customer_id, order_date
```

### Results/Findings
The analysis results are summarised as follows:
1. Customer A spent the most amount in the restaurant
2. Customer B is top visiting customer of the restaurant
3. Ramen is the most purchased item from the menu list and also most popular item bought by the customer

### Recommendations
- The point incentive encourage customers to become members and should be continued as it increases customer loyalty
- Invest in promotions and marketing to communicate benefits attached to being a member and encourage customer loyalty

### Limitations
- Data records should be made electronically e.g use of Excel workbook, as manual data entry are prone to error and reduce data integrity.

### References
1. [Youtube](https://www.youtube.com/watch?v=0N9xekdKCwk)
