Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
 FOR country
WITH CTE_1 AS (
    SELECT
        asn.country,
        SUM(CASE
            WHEN revenue IS NULL THEN (unit_price * units_sold)
            ELSE revenue
        END) AS rev_cal
    FROM all_sessions AS asn
    JOIN analytics USING (fullvisitorid, visitid)
    WHERE units_sold IS NOT NULL
    GROUP BY asn.country
)
SELECT
    country,
    rev_cal
FROM CTE_1
WHERE rev_cal IS NOT NULL
ORDER BY rev_cal DESC;


 
FOR CITY:

WITH CTE_1 AS (
    SELECT
        CASE
            WHEN city IN ('(not set)', 'not available in demo dataset') THEN country
            ELSE city
        END AS city_filled,
        SUM(CASE
            WHEN revenue IS NULL THEN (unit_price * units_sold)
            ELSE revenue
        END) AS rev_cal
    FROM all_sessions AS asn
    JOIN analytics USING (fullvisitorid, visitid)
    WHERE units_sold IS NOT NULL
    GROUP BY city_filled
)
SELECT
    city_filled AS city,
    rev_cal
FROM CTE_1
ORDER BY rev_cal DESC;




Answer:




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

FOR COUNTRY

select distinct als.country, avg (sr.total_ordered)
from all_sessions as als
join products as pd ON pd.sku = als.productsku
join sales_report as sr on pd.sku = sr.productsku
			   group by als.country
			   order by avg (sr.total_ordered) DESC

FOR CITY

select distinct als.city, avg (sr.total_ordered)
from all_sessions as als
join products as pd ON pd.sku = als.productsku
join sales_report as sr on pd.sku = sr.productsku
			   group by als.city
			   order by avg (sr.total_ordered) DESC




Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
for country

SELECT
    country,
    v2productcategory,
    COUNT(v2productcategory) AS category_count,
    RANK() OVER (PARTITION BY country ORDER BY COUNT(v2productcategory) DESC) AS category_rank
FROM all_sessions
GROUP BY country, v2productcategory
ORDER BY country DESC, category_rank;

For city

SELECT
    city_or_country AS city,
    v2productcategory,
    COUNT(v2productcategory) AS category_count,
    RANK() OVER (PARTITION BY city_or_country ORDER BY COUNT(v2productcategory) DESC) AS category_rank
FROM (
    SELECT
        CASE
            WHEN city IN ('not set', 'not available in demo dataset') THEN country
            ELSE city
        END AS city_or_country,
        v2productcategory
    FROM all_sessions
) AS sessions
GROUP BY city_or_country, v2productcategory
ORDER BY city DESC, category_rank;



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
For country:
WITH ProductSalesByCountry AS (
    SELECT
        CASE
            WHEN country = 'not set' THEN 'Unknown Country'
            ELSE country
        END AS country_group,
        v2productcategory,
        v2productname,
        SUM(a.units_sold) AS total_units_sold
    FROM all_sessions AS sessions
    JOIN analytics AS a ON sessions.fullvisitorid = a.fullvisitorid
                        AND sessions.visitid = a.visitid
    WHERE country <> 'not set' AND a.units_sold IS NOT NULL
    GROUP BY country_group, v2productcategory, v2productname
),
RankedProductsByCountry AS (
    SELECT
        ps.*,
        RANK() OVER (PARTITION BY ps.country_group ORDER BY ps.total_units_sold DESC) AS product_rank
    FROM ProductSalesByCountry ps
)
SELECT
    country_group AS country,
    v2productcategory,
    v2productname,
    total_units_sold,
    product_rank
FROM RankedProductsByCountry
WHERE product_rank = 1
ORDER BY country_group, total_units_sold DESC;










For city:

WITH ProductSalesByCity AS (
    SELECT
        CASE
            WHEN city IN ('not set', 'not available in demo dataset') THEN country
            ELSE city
        END AS city_group,
        v2productcategory,
        v2productname,
        SUM(a.units_sold) AS total_units_sold
    FROM all_sessions AS sessions
    JOIN analytics AS a ON sessions.fullvisitorid = a.fullvisitorid
                        AND sessions.visitid = a.visitid
    WHERE city IS NOT NULL AND a.units_sold IS NOT NULL
    GROUP BY city_group, v2productcategory, v2productname
),
RankedProductsByCity AS (
    SELECT
        ps.*,
        RANK() OVER (PARTITION BY ps.city_group ORDER BY ps.total_units_sold DESC) AS product_rank
    FROM ProductSalesByCity ps
)
SELECT
    city_group AS city,
    v2productcategory,
    v2productname,
    total_units_sold,
    product_rank
FROM RankedProductsByCity
WHERE product_rank = 1
ORDER BY city_group, total_units_sold DESC;



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

COUNTRY

WITH CTE_1 AS (
    SELECT
        asn.country,
        SUM(CASE
            WHEN revenue IS NULL THEN (unit_price * units_sold)
            ELSE revenue
        END) AS rev_cal
    FROM all_sessions AS asn
    JOIN analytics USING (fullvisitorid, visitid)
    WHERE units_sold IS NOT NULL
    GROUP BY asn.country
)
SELECT
    country,
    rev_cal
FROM CTE_1
WHERE rev_cal IS NOT NULL
ORDER BY rev_cal DESC;

FOR CITY

WITH CTE_1 AS (
    SELECT
        CASE
            WHEN city IN ('(not set)', 'not available in demo dataset') THEN country
            ELSE city
        END AS city_filled,
        SUM(CASE
            WHEN revenue IS NULL THEN (unit_price * units_sold)
            ELSE revenue
        END) AS rev_cal
    FROM all_sessions AS asn
    JOIN analytics USING (fullvisitorid, visitid)
    WHERE units_sold IS NOT NULL
    GROUP BY city_filled
)
SELECT
    city_filled AS city,
    rev_cal
FROM CTE_1
ORDER BY rev_cal DESC;




Answer:







