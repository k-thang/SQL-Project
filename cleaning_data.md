## What issues will you address by cleaning the data?

**Monetary Attributes** <br>
In response to the ‘hint’ provided, these queries divided these columns by 1,000,000 in order to provide a realistic view of the data.
```SQL
UPDATE analytics
SET unit_price = unit_price/1000000;
```
```SQL
UPDATE analytics 
SET revenue = revenue/1000000;
```
```SQL
UPDATE all_sessions
SET  product_price =  product_price/1000000;
```
```SQL
UPDATE all_sessions
SET  total_transaction_revenue = total_transaction_revenue/1000000;
```
```SQL
UPDATE all_sessions
SET  product_revenue =  product_revenue/1000000;
```

**Date Attributes** <br>
The attributes relating to dates were displayed as numbers that resemble dates. These queries transformed the numbers into a readable date format:
```SQL
ALTER TABLE all_sessions
ALTER COLUMN date TYPE DATE USING date::DATE;
```
```SQL
ALTER TABLE analytics
ALTER COLUMN date TYPE DATE USING date::DATE;
```
**Trimming Attributes** <br>
Some attributes had white space on the left hand side of the attribute’s data. The queries used to remove the white space are as follows: 
```SQL
UPDATE all_sessions
SET productvariant = LTRIM(productvariant);
```
```SQL
UPDATE products
SET name = LTRIM(name);
```
```SQL
UPDATE salesreport
SET name = LTRIM(name);
```
**Addressing Time Columns** <br>
The attribute `visit_starttime` in the analytics table was displayed in epoch time which is a recognized format. However, `time.all_sessions` displays a more ambiguous format. Until more information is provided, I would make the assumption that `all_sessions.time` is either incorrect or incomplete.

Therefore, I deleted `all_sessions.time` and will only reference `analytics.visit_starttime` to reduce redundancy in the database. The query to delete the aforementioned attribute is as follows:
```SQL
ALTER TABLE all_sessions
DROP COLUMN time;
```
Although the `analytics.visit_starttime` is correct, if I was presenting the data to a less technical audience then I would cast it to a more readable format. The query would be as follows:
```SQL
SELECT TO_TIMESTAMP(visit_starttime)::TIME
FROM analytics;
```
**Addressing Missing Data** <br>
Throughout the dataset there are various attributes that have mainly null values. If given a chance to collect more data points then the attributes could prove to be useful therefore, I did not delete any columns due to missing data.

If I were to receive more context about the data, I would delete attributes that don’t provide the relevant audiences with meaningful insights. An example of a column that I would potentially delete is `analytics.userid` since all the data is null. The query to delete said column is as follows:
```SQL
ALTER TABLE analytics
DROP COLUMN userid;
```
**Addressing Relational Database Setup** <br>
For the purposes of this project, the questions can be answered without altering the database setup. However, if given more time and context, I would overhaul the database’s five table structure by performing data normalization. Normalizing the data would involve breaking down large entities into smaller entities and linking them through primary/foreign keys which would:
* Improve the database’s scalability, adaptability and query performance.
* Minimize the redundancy and inconsistency in the database, leading to more accurate reporting and better business decisions.

Queries that are fundamental for data normalization are as follows: 
```SQL
-- Query to create a new tables from an existing one
CREATE TABLE new_table_name AS (
    SELECT column1,
           column2,
           columnN 
    FROM existing_table_name
); 
```
```SQL
-- Query to define a primary key
ALTER TABLE insert_table_name
ADD PRIMARY KEY (insert_attribute_name);
```
```SQL
-- Query to define a foreign key
ALTER TABLE insert_table_name
ADD FOREIGN KEY (insert_attribute_name) REFERENCES existing_table_name(existing_table_primarykey);
```
**Addressing Duplicate Data** <br>
Duplicate data was found in the analytics and all_sessions table. The following queries were executed in order to check for duplicates and then delete them:
```SQL
-- Query checks the entire analytics table for duplicate values
SELECT 	 COUNT(*), 
         visitnumber,
         visitid,
         visit_starttime,
         date,
         fullvisitorid,
         userid,
         channel_grouping,
         social_engagement_type,
         units_sold,
         pageviews,
         time_onsite,
         bounces,
         revenue,
         unit_price
FROM analytics
GROUP BY visitnumber, 
         visitid,
         visit_starttime,
         date,
         fullvisitorid,
         userid,
         channel_grouping,
         social_engagement_type,
         units_sold,
         pageviews,
         time_onsite,
         bounces,
         revenue,
         unit_price
HAVING COUNT(*) > 1;
```
```SQL
-- Query creates a new table that groups the duplicates using the PARTITION BY clause
CREATE TABLE new_analytics AS (
	SELECT *, 
               ROW_NUMBER() OVER (PARTITION BY visitnumber, 
                                               visitid, 
                                               visit_starttime, 
                                               date, 
                                               fullvisitorid, 
                                               userid, 
                                               channel_grouping, 
                                               social_engagement_type, 
                                               units_sold, 
                                               pageviews, 
                                               time_onsite, 
                                               bounces, 
                                               revenue, 
                                               unit_price									
               ORDER BY fullvisitorid) AS row_num					
	 FROM analytics
);
```
```SQL
-- Query deletes the duplicates in the new_analytics table
DELETE FROM new_analytics
WHERE row_num <> 1;
```
```SQL
-- Query deletes the column ‘row_num’ that was created through the PARTITION BY clause. This column is no longer needed once the duplicates have been deleted from the new table.
ALTER TABLE new_analytics
DROP COLUMN row_num;
```
