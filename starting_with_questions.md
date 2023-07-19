## Answer the following questions and provide the SQL queries
    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT country,
       SUM(transaction_revenue) AS total_transaction_revenue
FROM all_sessions
WHERE transaction_revenue is not null 
GROUP BY country
ORDER BY SUM(transaction_revenue) DESC;
```
```SQL
-- City Query
SELECT  city,
        SUM(transaction_revenue) AS total_transaction_revenue
FROM all_sessions
WHERE transaction_revenue is not null AND city NOT LIKE '%not%'
GROUP BY city
ORDER BY SUM(transaction_revenue) DESC;
```
Answer:
* The country with the highest level of transaction revenue on the site is the United States.
* The city with the highest level of transaction revenue on the site is Sunnyvale.

**Question 2: What is the average number of products ordered from visitors in each city and country?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT 	country,
        ROUND(AVG(product_quantity),2) AS avg_products_ordered
FROM all_sessions 
GROUP BY country
HAVING AVG(product_quantity) is NOT NULL
ORDER BY AVG(product_quantity) DESC;
```
```SQL
-- City Query
SELECT 	city,
        ROUND(AVG(product_quantity),2) AS avg_products_ordered
FROM all_sessions 
WHERE city NOT LIKE ('%not%')
GROUP BY city
HAVING AVG(product_quantity) is NOT NULL
ORDER BY AVG(product_quantity) DESC;
```
Answer:
Apart from certain cities in the USA and Spain, the average number of products sold from each city and country is 1.0

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT 	country,
        v2productcategory,
        SUM(product_quantity) AS products_ordered
FROM all_sessions 
WHERE v2productcategory NOT LIKE ('%not%')
GROUP BY country, v2productcategory
HAVING SUM(product_quantity) is NOT NULL
ORDER BY country DESC;
```
```SQL
-- City Query
SELECT 	city,
        v2productcategory,
        SUM(product_quantity) AS products_ordered
FROM all_sessions 
WHERE v2productcategory NOT LIKE ('%not%')
      AND city NOT LIKE ('%not%') 
GROUP BY city, v2productcategory
HAVING SUM(product_quantity) is NOT NULL
ORDER BY city DESC;
```
Answer: <br>
* The USA has the highest demand for products specifically “Notebooks” (65 purchases) and “Bags” (54 purchases)
* Cities in the USA have a tendency to purchase more electronics. Nest was the most popular product (13 purchases) spread out between 7 different USA cities.

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?** <br>
SQL Queries:
```SQL
-- Country Query
WITH order_totals AS (
  SELECT country,
         v2productname,
         SUM(product_quantity) AS total_product_quantity
  FROM all_sessions
  WHERE product_quantity > 0
  GROUP BY country, v2productname
),
row_nums AS (
  SELECT *, 
         ROW_NUMBER() OVER(PARTITION BY country ORDER BY ot.total_product_quantity) AS row_num
  FROM order_totals AS ot
)	
SELECT ot.country,
       rn.v2productname AS topselling_product, 
       MAX(ot.total_product_quantity) AS amount_sold
FROM order_totals AS ot
JOIN row_nums AS rn ON ot.country = rn.country
GROUP BY ot.country, rn.row_num, rn.v2productname
HAVING rn.row_num = 1;
```
```SQL
-- City Query
WITH order_totals AS (
  SELECT city,
         v2productname,
         SUM(product_quantity) AS total_product_quantity
  FROM all_sessions
  WHERE product_quantity > 0
  GROUP BY city, v2productname
),
row_nums AS (
  SELECT *, 
         ROW_NUMBER() OVER(PARTITION BY city ORDER BY ot.total_product_quantity) AS row_num
  FROM order_totals AS ot
)	
SELECT ot.city,
       rn.v2productname AS topselling_product, 
       MAX(ot.total_product_quantity) AS amount_sold
FROM order_totals AS ot
JOIN row_nums AS rn ON ot.city = rn.city
GROUP BY ot.city, rn.row_num, rn.v2productname
HAVING rn.row_num = 1;
```
Answer:
* Both cities and countries tend to purchase apparel and electronics. 
* Many cities within the USA order products whereas the orders from other countries like Spain or Ireland are heavily concentrated in 1 city.

**Question 5: Can we summarize the impact of revenue generated from each city/country?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT country,
       SUM(product_price*product_quantity) AS revenue
FROM all_sessions AS sessions
GROUP BY country
HAVING SUM(product_price*product_quantity) is NOT NULL
ORDER BY SUM(product_price*product_quantity) DESC;
```
```SQL
-- City Query
SELECT city,
       SUM(product_price*product_quantity) AS revenue
FROM all_sessions AS sessions
WHERE city is NOT NULL
GROUP BY city
HAVING SUM(product_price*product_quantity) is NOT NULL
ORDER BY SUM(product_price*product_quantity) DESC;
```
Answer:
* The USA generates the most amount of revenue by a large margin. The USA maintains this position through cities like Mountain View, Salem and New York that generate much more revenue than non-US cities.
* Other countries don’t generate the same level of revenue since they have only 1-2 cities that purchase products.







