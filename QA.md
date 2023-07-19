## What are your risk areas? Identify and describe them.
The biggest area of risk was not being able to identify if the issues were caused from upstream failures. For the purposes of this project, the short-term solution was to clean the data using various transformation techniques. However, if I had more time and context, I would dig deeper into the reasons behind the vast amounts of duplicate/null data and correct the upstream issues as a longer-term solution.

**Duplicate Data Risk** <br>
Specifically in the ‘analytics’ and ‘all_sessions’ tables, there was a lot of duplicated data. One of the biggest causes for concern was that the attributes that appeared to be primary keys (e.g. `fullvisitorid`) had duplicates. 

Duplicate data holds risks such as:
* Inhibits accuracy and can skew results used to make business decisions 
* Unnecessarily reduces query optimization and increase storage costs 

**Missing Values Risk** <br>
Within the dataset there were many null values that impeded my ability to produce complete and accurate reports. For example, `all_sessions.city` had many null values that made it difficult to determine the most accurate answers to the given questions [(starting_with_questions.md)](/starting_with_questions.md). Therefore, the city with the highest transaction revenue couldn’t be identified because that row’s city value was not available. As a result, the answer given was technically for the second highest transaction revenue instead.

The existence of null values doesn’t necessarily mean the data is of lower quality but it’s important to acknowledge that null values can hold a level of risk.

**Data Type Risk** <br>
A component of my data cleaning was dividing relevant monetary attributes by 1M (e.g. `analytics.unit_price`, `all_sessions.product_price`). Originally, I selected integer as the datatype for these attributes which caused the output to be less accurate had I chosen the datatype to be numeric.

Having precise data is essential since we’re dealing with customers, invoices and refunds. If I had to repeat the process, I would have chosen a data type that would translate into a more accurate output (e.g. numeric).

## QA Process: <br> Describe your QA process and include the SQL queries used to execute it.
My QA process involved testing several data quality dimensions on the dataset. Using many data assurance queries, I was able to spot problems in the data and clean them accordingly. 

For simplicity sake, I will only show one version of each query but the formula for the query has been executed across all relevant tables and their respective attributes during the QA process. 

Please note that the process below does not include the cleaning queries - you can find those queries on the [cleaning_data.md](/cleaning_data.md).

**Completeness - are there missing values in the dataset?** <br>
There were many attributes that contained null or unavailable values. The queries below are examples of how I tested for null values within the dataset:
```SQL
-- Find null values
SELECT userid
FROM  analytics
WHERE userid IS NULL;
```
```SQL
-- Find unavailable values in all_sessions.city. The following query will find the values that read "not available in demo dataset” and “(not set)”
SELECT city
FROM  all_sessions
WHERE city LIKE ('%not%');
```
It’s crucial to ensure ‘completeness’ within the dataset as it allows me to:
* Ensure that primary keys don’t have null values 
* Determine if an attribute has too many nulls (i.e. a low quality score) that would prevent the audience from identifying meaningful insights.
* Evaluate the reason/meaning behind the null values and determine if there are upstream failures.

**Accuracy - did the values in the dataset represent real-world values?** <br>
“Garbage in, garbage out” - any meaningful insights drawn from a dataset must come from accurate data. I tested for accuracy within the dataset by validating that the data values were within an expected range.

For example, the money amounts in the dataset (e.g. `analytics.unit_price`, `all_sessions.product_price`, etc.) were not representative of real world values. These amounts were $1M times greater than would have been realistic for the products. As a result, a “Youtube Notebook and Pen Set” was listed as $7,990,000. When divided by $1M, it computes a more realistic price of $7.99.

These queries were used to determine if the values fall within an expected range:
```SQL
SELECT product_price,
       v2productname
FROM all_sessions
WHERE product_price < 100;
```
```SQL
SELECT product_price,
       v2productname
FROM all_sessions
WHERE product_price > 100
      AND product_price < 500;
```
```SQL
SELECT product_price,
       v2productname
FROM all_sessions
WHERE product_price > 1000000;
```
**Uniqueness - are there duplicates in the dataset where there shouldn’t be?** <br>
Testing for uniqueness is important for many reasons such as:
* Identifying a table’s primary key
* Ensuring that duplicate data is removed to prevent biased or misleading results which build trust in the data analysis 

For example, `salesbysku.productsku` is likely the primary key. To test this theory, you would run the following query:
```SQL
SELECT productsku,
       COUNT(productsku) AS productsku_count
FROM salesbysku
GROUP BY productsku
HAVING COUNT(productsku) > 1;
```
If the output indicates that the attribute is not unique then either the attribute is not the primary key or the data needs to be cleaned further to remove duplicates.
