What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.
QA PROCESS

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
