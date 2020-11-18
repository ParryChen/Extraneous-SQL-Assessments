### Summary
---

In this repo, you will find a variety of completed SQL assessments. You can find a written assessment below. 

### Assessment
---

1. In a single SQL query, return the total sales for each Brand Name owned by Beam Suntory (holding company) for each and every Date between 3/13/2020 and 6/27/2020 inclusive. 

SELECT B.BRAND_NAME, SUM(O.UNIT_PRICE*O.QUANTITY) AS TOTALSALES
FROM Drizly Brands B
JOIN Store_Order_Items O ON B.BRAND_ID = O.BRAND_ID
WHERE HOLDING_COMPANY_NAME = 'Beam Suntory' AND (O.Date BETWEEN '2020/03/13' AND '2020/09/20')
GROUP BY B.BRAND_NAME;

2. For each Product (ID), determine the “store of sale” that made the first sale of the product in 2019. 

SELECT O.PRODUCT_ID, O.STORE_ID
FROM Store_Order_Items O
INNER JOIN
    (SELECT PRODUCT_ID, STORE_ID, MIN(DATE) AS MinDateTime
    FROM Store_Order_Items
    WHERE YEAR(DATE) = '2019'
    GROUP BY PRODUCT_ID) groupedtt 
ON O.PRODUCT_ID = groupedtt.PRODUCT_ID 
AND O.DATE = groupedtt.MinDateTime;

3. Someone was trying to add up orders by store within the month of December 2019 using the following query: SELECT store_id, count(*) as total_orders FROM store_order_items WHERE date between ‘12/1/19’::date and ‘12/31/19’::date GROUP BY 1 However the stores are saying that they did not receive that many orders, why? 

The date format is incorrect, it should be YYYY/MM/DD. Postgres will automatically try to convert incorrect date formats

4. Using Store Order Items, write a query that will output one row for each Order ID with a column denoting whether or not the month name of the order date ends in “r”, as well as a column for whether or not the order contained at least one product with a requested quantity greater than 1. 

SELECT ORDER_ID, 
CASE WHEN MONTHNAME(DATE) like '%r' THEN 1 ELSE 0 END AS Ends_In_R,
CASE WHEN QUANTITY > 1 THEN 1 ELSE 0 END AS More_Than_One;

5. How many users placed their third order containing a product owned by the Diageo holding company on or after 7/15/20? Only consider orders containing at least one Diageo holding company product. 

SELECT *
FROM (
    SELECT O.*,
        row_number() OVER (
            PARTITION BY USER_ID 
            ORDER BY DATE
            ) as rank
    FROM Store_Order_Items O
    WHERE DATE >= '2020/07/15'
    ) O
JOIN Drizly Brands B ON O.BRAND_ID = B.BRAND_ID
WHERE O.rank = 3 AND B.HOLDING_COMPANY_NAME = 'Diageo';
