## What are your risk areas? Identify and describe them.
The biggest area of risk was not being able to fix the root cause of the data’s quality issues. For the purposes of this project, the short-term solution was to clean the data using various transformation techniques. However, if I had more time and context, I would dig deeper into the reasons behind the vast amounts of duplicate/null data and correct the upstream failures as a longer-term solution.

**Duplicate Data Risk** <br>
The analytics table contained millions of duplicate rows which had been deleted during my data cleaning. However, identiying the root cause is crucial since duplicates: 
* Inhibit accuracy and can skew results used to make business decisions 
* Unnecessarily reduces query optimization
  
**Missing Values Risk** <br>
Within the dataset there were many null values that impeded my ability to produce complete and accurate reports. For example, `new_all_sessions.city` had many null values that made it difficult to determine the most accurate answers to the given questions in [starting_with_questions.md](/starting_with_questions.md). Therefore, the city with the highest transaction revenue couldn’t be identified because that row’s city value was not available. As a result, the answer given was technically for the second highest transaction revenue instead.

The existence of null values doesn’t necessarily mean the data is of lower quality but it’s important to acknowledge that null values can hold a level of risk.

**Data Type Risk** <br>
A component of my data cleaning was dividing relevant monetary attributes by 1,000,000 (e.g. `new_analytics.unit_price`, `new_all_sessions.product_price`). Originally, I selected integer as the datatype for these attributes which caused the output to be less accurate had I chosen the datatype to be numeric.

Having precise data is essential since we’re dealing with customers, invoices and refunds. If I had to repeat the process, I would have chosen a data type that would translate into a more accurate output (e.g. numeric).

## QA Process
My QA process involved testing several data quality dimensions on the dataset. Using many data assurance queries, I was able to spot problems in the data and clean them accordingly. 

For simplicity sake, I will only show one version of each query but the formula for the query has been executed across all relevant tables and their respective attributes during the QA process. 

**Completeness - are there missing values in the dataset?** <br>
There were many attributes that contained null or unavailable values. The queries below are examples of how I tested for null values within the dataset:
```SQL
-- Find null values
SELECT userid
FROM  new_analytics
WHERE userid IS NULL;
```
```SQL
-- Find unavailable values in new_all_sessions.city. The following query will find the values that read "not available in demo dataset” and “(not set)”
SELECT city
FROM  new_all_sessions
WHERE city LIKE ('%not%');
```
It’s crucial to ensure ‘completeness’ within the dataset as it allows me to:
* Ensure that primary keys don’t have null values 
* Determine if an attribute has too many nulls (i.e. a low quality score) that would prevent the audience from identifying meaningful insights.
* Evaluate the reason/meaning behind the null values and determine if there are upstream failures.

**Accuracy - did the values in the dataset represent real-world values?** <br>
“Garbage in, garbage out” - any meaningful insights drawn from a dataset must come from accurate data. I tested for accuracy within the dataset by validating that the data values were within an expected range.

For example, the money amounts in the dataset were not representative of real world values. These amounts were 1,000,000 times greater than would have been realistic for the products. As a result, a “Youtube Notebook and Pen Set” was listed as $7,990,000. When divided by 1,000,000, it computes a more realistic price of $7.99.

These queries were used to determine if the values fell within an expected range:
```SQL
SELECT product_price,
       v2productname
FROM new_all_sessions
WHERE product_price < 100;
```
```SQL
SELECT product_price,
       v2productname
FROM new_all_sessions
WHERE product_price > 100
      AND product_price < 500;
```
```SQL
SELECT product_price,
       v2productname
FROM new_all_sessions
WHERE product_price > 1000000;
```
**Uniqueness - are there duplicates in the dataset where there shouldn’t be?** <br>
Testing for uniqueness is important for many reasons such as:
* Identifying a table’s primary key
* Ensuring that duplicate data is removed to prevent biased or misleading results

For example, `salesbysku.productsku` is likely the primary key. To test this theory, you would run the following query:
```SQL
SELECT productsku,
       COUNT(productsku) AS productsku_count
FROM salesbysku
GROUP BY productsku
HAVING COUNT(productsku) > 1;
```
If the output indicates that the attribute is not unique then either the attribute is not the primary key or the data needs to be cleaned further to remove duplicates.
