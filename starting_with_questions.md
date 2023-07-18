Answer the following questions and provide the SQL queries used to find the answer.
    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT country, SUM(transaction_revenue) AS total_transaction_revenue
FROM all_sessions
WHERE transaction_revenue is not null 
GROUP BY country
ORDER BY SUM(transaction_revenue) DESC;
```
```SQL
-- City Query
SELECT  city, SUM(transaction_revenue) AS total_transaction_revenue
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
        ROUND(AVG(product_quantity),2) AS “Average Products Ordered”
FROM all_sessions 
GROUP BY country
HAVING AVG(product_quantity) is NOT NULL
ORDER BY AVG(product_quantity) DESC;
```
```SQL
-- City Query
SELECT 	city,
        ROUND(AVG(product_quantity),2) AS "Average Products Ordered"
FROM all_sessions 
WHERE city NOT LIKE ('%not%')
GROUP BY city
HAVING AVG(product_quantity) is NOT NULL
ORDER BY AVG(product_quantity) DESC;
```
Answer:






**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT 	country,
        v2productcategory,
        SUM(product_quantity) AS "Products Ordered"
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
        SUM(product_quantity) AS "Products Ordered"
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

```
```SQL

```
Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?** <br>
SQL Queries:
```SQL
-- Country Query
SELECT  country,
        SUM(product_price*product_quantity) AS revenue
FROM all_sessions AS sessions
GROUP BY country
HAVING SUM(product_price*product_quantity) is NOT NULL
ORDER BY SUM(product_price*product_quantity) DESC;
```
```SQL
-- City Query
SELECT  city,
        SUM(product_price*product_quantity) AS revenue
FROM all_sessions AS sessions
WHERE city is NOT NULL
GROUP BY city
HAVING SUM(product_price*product_quantity) is NOT NULL
ORDER BY SUM(product_price*product_quantity) DESC;
```
Answer:







