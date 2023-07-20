## Question 1: Find the number of unique visitors for each channel grouping. What does the output tell you?
SQL Queries:
```SQL
SELECT channel_grouping AS channel,
       COUNT(DISTINCT(fullvisitorid)) AS unique_visitors
FROM new_all_sessions
GROUP BY channel_grouping
ORDER BY channel_grouping;
```
Answer: 
* Most unique visitors visit the site through “organic search” (8027 visitors) which indicates that the site has been optimized for SEO (search engine optimization).
* Unique visitors enter the site through the “direct” (2805 visitors) and “referral’ channels (2419 visitors) which indicates strong brand awareness.
* The other channel groupings are less successful at bringing in unique visitors which would indicate a need to shift strategy in these areas.

## Question 2: Which channel grouping generates the most amount of revenue?
SQL Queries:
```SQL
SELECT channel_grouping,
       SUM(product_quantity*product_price) AS revenue
FROM new_all_sessions
WHERE product_quantity > 0
GROUP by channel_grouping
ORDER BY SUM(product_quantity*product_price) DESC;
```
Answer:
Referrals generate the most amount of revenue, followed by the organic search and direct channels. Considering word of mouth is a revenue generator then the site should start requesting customer testimonies and offering referral bonuses to boost referral sales.
## Question 3: How is the revenue distributed by month? Are there any patterns?
SQL Queries:
```SQL
SELECT EXTRACT(MONTH from sessions.date) AS month,
       SUM(product_price*product_quantity) AS revenue
FROM new_all_sessions AS sessions
GROUP BY EXTRACT(MONTH from sessions.date)
ORDER BY EXTRACT(MONTH from sessions.date);
```
Answer:
On average, the last month of each quarter is the most profitable (March, June, Sept, Dec). Possibly due to the sales team attempting to get their quotas completed before the quarter end.

## Question 4: Rank the countries by the amount of money spent by visitors in 2016 and 2017 respectively. Identify the country that is ranked the highest in 2016 and 2017.
SQL Queries:
```SQL
-- Ranks countries by total revenue in 2016
WITH sales_totals AS (
  SELECT country,
         SUM(product_price*product_quantity) AS total_sales,
         DENSE_RANK() OVER (ORDER BY SUM(product_price*product_quantity) DESC) AS sales_rank
  FROM new_all_sessions
  WHERE product_quantity > 0
  AND EXTRACT(YEAR FROM date) = 2016
  GROUP BY country
)
SELECT sessions.country,
       st.sales_rank,
       st.total_sales
FROM new_all_sessions AS sessions
JOIN sales_totals AS st ON sessions.country = st.country
GROUP BY sessions.country, st.sales_rank, st.total_sales
ORDER BY st.sales_rank;
```
```SQL
-- Ranks countries by total revenue in 2017
WITH sales_totals AS (
  SELECT country,
         SUM(product_price*product_quantity) AS total_sales,
         DENSE_RANK() OVER (ORDER BY SUM(product_price*product_quantity) DESC) AS sales_rank
  FROM new_all_sessions
  WHERE product_quantity > 0
  AND EXTRACT(YEAR FROM date) = 2017
  GROUP BY country
)
SELECT sessions.country,
       st.sales_rank,
       st.total_sales
FROM new_all_sessions AS sessions
JOIN sales_totals AS st ON sessions.country = st.country
GROUP BY sessions.country, st.sales_rank, st.total_sales
ORDER BY st.sales_rank;
```
Answer:
The USA ranked the highest in money spent in 2016 and 2017. The USA spent 2x as much in 2017 than in 2016 which indicates an increased demand for this site’s products in the USA.

## Question 5: What is the average time spent on the site for the visitors that made purchases versus those that did not.
SQL Queries:
```SQL
WITH identify_purchase_activity AS (
	SELECT time_onsite,
         CASE
           WHEN product_quantity is NULL THEN 'no_purchase_made'
           ELSE 'purchase_made'
         END AS purchase_activity
	FROM new_all_sessions
	),
visit_time AS (
  SELECT time_onsite,
         purchase_activity
  FROM identify_purchase_activity 
  WHERE time_onsite is NOT NULL
  GROUP BY time_onsite, purchase_activity
)
SELECT purchase_activity,
       ROUND(AVG(time_onsite),2) AS average_time_onsite
FROM visit_time 
GROUP BY purchase_activity;
```
Answer:
* Visitors that made a purchase spent an average of 6.23 minutes on the site.
* Visitors that didn’t make a purchase spent an average of 13.44 minutes on the site.
