What issues will you address by cleaning the data?

•	Rectifying column data types
•	Eliminating duplicate records
•	Excluding redundant, empty, and unrelated columns
•	Scaling down oversized values within columns and normalizing data
•	Addressing structural irregularities
•	Managing absent data and filling in gaps


>query to  find any duplicate row
select distinct fullvisitorid 
	from all_sessions


>Query to find null values in a column:
select productrefundamount  
from all_sessions
where productrefundamount is not null

>Queries to find redundant columns and removing them

select itemquantity from all_sessions
where itemquantity is not null;

ALTER TABLE all_sessions
DROP COLUMN itemquantity;

select productrevenue from all_sessions
where productrevenue is not null;

ALTER TABLE all_sessions
DROP COLUMN productrevenue

select searchkeyword from all_sessions
where searchkeyword is not null

ALTER TABLE all_sessions
DROP COLUMN searchkeyword 


>Queries for  cleaning structural issues: replacing 'not set' with null 



UPDATE all_sessions
SET productvariant = null
WHERE productvariant = '(not set)'

UPDATE all_sessions
SET currencycode = 'USD'
WHERE currencycode = ' ' OR currencycode is NULL

> Queries for handling missing values
--filling city name with country name if city name not available
update all_sessions
set city = 
    case
       when city = '(not set)' then country
       when city = 'not available in demo dataset' then country
else city
end

--filling missing values of revenue record where unitprice and unit sold are available
select revenue,
case
when revenue is null then (unit_price * units_sold)
else revenue
end as rev_corrected
from analytics
where units_sold is not null

>correcting values like:  product_price 

UPDATE all_sessions 
SET "productprice" = "productprice"/1000000




Queries:
1.	Checking null values

	SELECT COUNT(*) AS null_count
	FROM products
	WHERE sku IS NULL;

2.	Identifying Data Duplication:
	
	SELECT visitid, COUNT(*) AS duplicate_count
	FROM all_sessions
	GROUP BY visitid
	HAVING COUNT(*) > 1;

3.	Cross referencing

		SELECT DISTINCT sku
		FROM products
		WHERE sku NOT IN (SELECT productsku FROM sales_report);


4.	JOIN Validation 


		SELECT COUNT(*) AS mismatch_count
		FROM sales_report as sr
		LEFT JOIN sales_by_sku as sbs ON sr.productsku = sbs.productsku
		WHERE sbs.productsku IS NULL;

