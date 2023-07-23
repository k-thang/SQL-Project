# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The goal is to attain insights into an e-commerce's store data through the use of various transformation and analysis techniques. These findings will be used to craft actionable next steps in order to increase the site’s revenue.

## Process
1. Created a database using PgAdmin and the provided [csv files](https://drive.google.com/drive/folders/1efDA4oc9w-bTbAvrESdOJpg9u-gEUBhJ).
2. Cleaned the data using various transformations. Review details and queries on [cleaning_data.md](cleaning_data.md).
3. Developed queries to attain insights on the data. Exact questions, queries and answers can be found in [starting_with_questions.md](starting_with_questions.md) and [starting_with_data.md](starting_with_data.md).
4. Identified the database’s risk areas and crafted a quality assurance process which tested several quality assurance dimensions. A detailed description of this process can be found in [QA.md](QA.md).

## Results
Key business insights: 
* The site’s most profitable channel is referrals. Based on this finding, the business should utilize its existing customer base to request customer testimonials and develop incentives for referring customers as a low cost way to yield returns.
* The USA has the highest demand for products over any other country. The data indicated that more than 15 cities within the USA order products from the site whereas the orders from other countries are only concentrated in 1-2 cities. The business should review strategies that emphasize geographical expansion within target countries in order to maximize profits and achieve economies of scale.
* Apart from certain cities in the USA and Spain, the average number of products sold from each city and country is 1.0. The business should incentive purchasers to buy more items per visit by offering free shipping with minimum purchase or volume discounts to increase wallet share.

More discoveries can be found in [starting_with_questions.md](starting_with_questions.md) and [starting_with_data.md](starting_with_data.md).

## Challenges 
* Interpreting the relationships between entities and the purpose of each attribute. Without any context, selecting data types and transforming the data was a challenge.
* Cleaning the data given the vast amounts of null and duplicate values.
* Determining primary keys (PK) as expected PK attributes contained duplicates.

## Future Goals
* Perform data normalization on the dataset in order to establish the relationships between entities and link them through primary/foreign keys.
* Identify upstream failures in order to find long-term solutions to problems in the database (e.g. null and duplicate values).
* Create visualizations to showcase findings.

