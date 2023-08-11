1.	FILLING INFO FOR CITIES WHEN NOT AVAILABLE WITH THE NAME OF THE COUNTRY

SELECT city, 
		CASE
		WHEN city = '(not set)' THEN country
		WHEN city = 'not available in demo dataset' THEN country
		ELSE city
		END AS city_filled
FROM all_sessions

2.	For filling revenue in the analytics table

SELECT revenue, 
		CASE
		WHEN revenue is null then (unit_price * units_sold)
		ELSE revenue
		END as rev_corrected
from analytics
where units_sold is not null

3.	Total number of visitors from India

	with cte as 
(select distinct fullvisitorid, country
from all_sessions
where country = 'India')

select count(*)
from cte

4.	Products having zero stocklevel and totalordered also zero

SELECT name, productsku, stocklevel, total_ordered
FROM sales_report
WHERE stocklevel = '0' and total_ordered = '0'

5. total footfall on the website during month of june in year 2017

SQL Queries: 

with CTE as (
select *
from analytics
where date between '2017-06-01' and '2017-06-30')

select sum(visit_number)
from CTE
