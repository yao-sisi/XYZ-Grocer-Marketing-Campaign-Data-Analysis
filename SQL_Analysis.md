Data Analysis in MySQL

```

/*
Step 1 - data cleaning and preparation
- Look for missing values
- Check the texts in categorical columns
- Sum up MntFruits, MntMeatProducts, MntFishProducts, MntSweetProducts into one column - MntGroceries
- Sum up Kidhome and Teenhome into one column - Childhome
- Sum up all Mnt columns into one column - MntTotal
*/

SELECT DISTINCT MaritalStatus
FROM marketing_data;

-- Group "Married", "Together" into "Coupled"
-- Group "YOLO", "Absurd" into "Other"
-- Group "Single", "Alone", "Widow", "Divorced" into "Single"

UPDATE marketing_data
SET MaritalStatus = 'Coupled'
WHERE MaritalStatus = 'Married' OR MaritalStatus = 'Together';


UPDATE marketing_data
SET MaritalStatus = 'Other'
WHERE MaritalStatus IN ('YOLO','Absurd');

UPDATE marketing_data
SET MaritalStatus = 'Single'
WHERE MaritalStatus IN ('Single','Alone','Widow','Divorced');

SELECT 
  MIN(YearBirth),
  MAX(YearBirth)
FROM marketing_data;

SELECT DISTINCT Education
FROM marketing_data;

-- Replace '2n Cycle' and 'Graduation' with 'Master'

UPDATE marketing_data
SET Education = 'Master'
WHERE Education IN ('2n Cycle','Graduation');

SELECT *
FROM marketing_data
WHERE Income = '';

-- These missing data represent customers who either have children, or are coupled up, or both
-- Assumption: they have no income
-- Solution: replace '' with 0

UPDATE marketing_data
SET Income = 0
WHERE Income = '';

-- Change the data type from TEXT to INT

ALTER TABLE marketing_data
CHANGE COLUMN `Income` `Income` INT NULL DEFAULT NULL;

SELECT DISTINCT Kidhome, Teenhome
FROM marketing_data;

SELECT DISTINCT Country
FROM marketing_data;

-- Sum up MntFruits, MntMeatProducts, MntFishProducts, MntSweetProducts into one column - MntGroceries
-- Sum up Kidhome and Teenhome into one column - Childhome
-- Sum up all Mnt columns into one column - MntTotal

ALTER TABLE marketing_data
ADD COLUMN MntGroceries INT AFTER MntSweetProducts;

UPDATE marketing_data
SET MntGroceries = MntFruits + MntMeatProducts + MntFishProducts + MntSweetProducts;

ALTER TABLE marketing_data
ADD COLUMN Childhome INT AFTER Teenhome;

UPDATE marketing_data
SET Childhome = Kidhome + Teenhome; 

ALTER TABLE marketing_data
ADD COLUMN MntTotal INT AFTER MntGoldProds;

UPDATE marketing_data
SET MntTotal = MntWines + MntGroceries + MntGoldProds;

-- Make a copy of the cleaned data, then drop irrelevant columns

CREATE TABLE marketing_cleaned AS
SELECT *
FROM marketing_data;

ALTER TABLE marketing_cleaned
DROP COLUMN YearBirth, 
DROP COLUMN Kidhome, 
DROP COLUMN Teenhome, 
DROP COLUMN MntFruits, 
DROP COLUMN MntMeatProducts, 
DROP COLUMN MntFishProducts, 
DROP COLUMN MntSweetProducts;


/*
Step 2 - Find our most valuable target segment
- Definition of "valuable" segment: segment that represents a large enough % of Total Revenue and where Revenue per Customer is high
- Options to slice the data: by Education, Childhome, MaritalStatus
- Income was not chosen as a way to slice the data because the information is not straightforward to collect, and income alone does not tell us about customers' spending power (eg. customers with high income but a lot of kids to spend money on)
*/

-- % of Total Revenue by Education

SET @total_rev := 0;
SELECT SUM(MntTotal) INTO @total_rev FROM marketing_cleaned;

SELECT
  Education,
  SUM(MntTotal) AS cat_rev,
  SUM(MntTotal) / @total_rev * 100 AS prct
FROM marketing_cleaned
GROUP BY Education
ORDER BY prct DESC
;

-- Master (75.6%) > PhD (24.1%) > Basic (0.3%)

-- Revenue per Customer by Education

SELECT
  Education,
  SUM(MntTotal) / COUNT(*) AS rev_per_cust
FROM marketing_cleaned
GROUP BY Education
ORDER BY rev_per_cust DESC
;

-- PhD (672) > Master (603) > Basic (82)

-- By Education, our most valuable segment is the "Master" segment: it represents more than 75% of our total revenue, and Revenue per Customer ranked second place and came very close (603 vs 672)

-- % of Total Revenue by # of Children

SELECT
  Childhome,
  SUM(MntTotal) AS cat_rev,
  SUM(MntTotal) / @total_rev * 100 AS prct
FROM marketing_cleaned
GROUP BY Childhome
ORDER BY prct DESC
;

-- 0 (52%) > 1 (39%) > 2 (8%) > 3 (1%)

-- Revenue per Customer by # of Children

SELECT
  Childhome,
  SUM(MntTotal) / COUNT(*) AS rev_per_cust
FROM marketing_cleaned
GROUP BY Childhome
ORDER BY rev_per_cust DESC
;

-- 0 (1,106) > 1 (473) > 3 (275) > 2 (246)

-- Childfree customers make up more than a half of total revenue and spend more than customers with children by far, followed by customers with only one child

-- % of Total Revenue by MaritalStatus

SELECT
  MaritalStatus,
  SUM(MntTotal) AS cat_rev,
  SUM(MntTotal) / @total_rev * 100 AS prct
FROM marketing_cleaned
GROUP BY MaritalStatus
ORDER BY prct DESC
;

-- Coupled (63.6%) > Single (36.1%) > Other (0.3%)

-- Revenue per Customer by MaritalStatus

SELECT
  MaritalStatus,
  SUM(MntTotal) / COUNT(*) AS rev_per_cust
FROM marketing_cleaned
GROUP BY MaritalStatus
ORDER BY rev_per_cust DESC
;

-- Other (808) > Single (620) > Coupled (598)

-- there is little difference in revenue per customer among Single and Coupled, while Other spends more on average, it only represent 0.3% of our revenue

-- Our most valuable segment is therefore: Master level education, childfree and coupled up

/*
Step 3 - Analyze campaign performance among target segment with Campaign Success Rate
*/
  
SELECT
  ROUND(SUM(AcceptedCmp1) / COUNT(*) * 100,2) AS Cmp1_Success,
  ROUND(SUM(AcceptedCmp2) / COUNT(*) * 100,2) AS Cmp2_Success,
  ROUND(SUM(AcceptedCmp3) / COUNT(*) * 100,2) AS Cmp3_Success,
  ROUND(SUM(AcceptedCmp4) / COUNT(*) * 100,2) AS Cmp4_Success,
  ROUND(SUM(AcceptedCmp5) / COUNT(*) * 100,2) AS Cmp5_Success
FROM marketing_data
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
;

-- Campaign 1 (19.86%) and 5 (22.95%) are the most successful and accepted by our target segment and should be prioritized and campaign 2, 3, 4 should be paused

/*
Step 4 - Investigate how to make it easier for target segment customers to make their purchases (deals, product type, means of purchase)
*/

-- Analyze what kind of products do our target segment customers spend the most on

SELECT
  ROUND(SUM(MntWines) / SUM(MntTotal) * 100,2) AS wine_prct,
  ROUND(SUM(MntGroceries) / SUM(MntTotal) * 100,2) AS groc_prct,
  ROUND(SUM(MntGoldProds) / SUM(MntTotal) * 100,2) AS gold_prct
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
;

-- 52.32% of total revenue from target segment is from groceries (fruits, meat, etc) and 41.38% comes from wine purchases

-- It is worth noting that although more revenue is brought in here from grocery sales, wine products most likely have better margin, given that our target customers do spend 41.38% on wine which shows interest and demand (vs demand for groceries is fixed and inelastic), we should make this the focus

-- Analyze whether target customers are taking advantage of our deals

-- Calculate the % of customers among target segment who have made purchases using deals at least once

SELECT
  ROUND(COUNT(CASE WHEN NumDealsPurchases > 0 THEN 1 ELSE NULL END) / COUNT(*) * 100,2) AS cust_deal_prct
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
;

-- 95.89% customers have taken advantage of deals

-- Analyze purchase methods

-- Calculate Utilization Rate-% of purchases made through a sales channel

SELECT
  ROUND(SUM(NumWebPurchases)/(SUM(NumWebPurchases)+SUM(NumCatalogPurchases)+SUM(NumStorePurchases))*100,2) AS web_prct,
  ROUND(SUM(NumCatalogPurchases)/(SUM(NumWebPurchases)+SUM(NumCatalogPurchases)+SUM(NumStorePurchases))*100,2) AS cat_prct,
  ROUND(SUM(NumStorePurchases)/(SUM(NumWebPurchases)+SUM(NumCatalogPurchases)+SUM(NumStorePurchases))*100,2) AS str_prct
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
;

-- store (44.58%) > catalog (29.20%) > web (26.22%) - an In-store campaign should be the first choice

-- Analyze customer complaints

-- Complaint rate - % of customers who have made complaints (in the past two years)

SELECT
  Complain,
  COUNT(*) AS cnt,
  COUNT(*)/SUM(COUNT(*)) OVER () AS prct
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
GROUP BY Complain
;

-- only 1% of customers in target segment have complained in the past two years

-- % of revenue by complaint status

SELECT
  Complain,
  SUM(MntTotal) AS rev,
  SUM(MntTotal)/SUM(SUM(MntTotal)) OVER () AS prct
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
GROUP BY Complain
;
  
-- More than 99% of revenue came from satisfied customers (those who did not complain in the past two years)

-- Analyze Revenue per Customer by complaint status

SELECT
  Complain,
  SUM(MntTotal) / COUNT(*) AS rev_per_cust
FROM marketing_cleaned
WHERE Education = 'Master'
  AND Childhome = 0
  AND MaritalStatus = 'Coupled'
GROUP BY Complain
;

-- Satisfied customers also tend to spend more (1,097 vs 608) than dissatisfied ones



```
