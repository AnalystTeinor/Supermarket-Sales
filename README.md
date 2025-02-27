# Supermarket-Sales
Data-driven decision-making to boost business growth and profitability will be made possible by the project's actionable insights on customer purchasing behavior, inventory management, revenue trends, and operational efficiency.

### Tool used
- SQL

### Query

```sql
Select*
from supermarket_sales;

Create table supermarket_sales_dup
like supermarket_sales;

Insert supermarket_sales_dup
Select*
from supermarket_sales;

Select*
from supermarket_sales_dup;

 ----------------------------------- Data Cleaning ----------------------


Select*,
row_number() over( partition by
invoice_id, branch, city, customer_type, gender, product_line, quantity, tax, total, 'date', 
'time', payment, cogs, gross_margin_percentage, gross_income, rating) as row_num
from supermarket_sales_dup;

With duplicate_cte as
(
Select*,
row_number() over( partition by
invoice_id, branch, city, customer_type, gender, product_line, quantity, tax, total, 'date', 
'time', payment, cogs, gross_margin_percentage, gross_income, rating) as row_num
from supermarket_sales_dup) 

Select*
from duplicate_cte
where row_num >1;

 ----------------------------------- Feature Engineering ------------------------------


Select `date`,
str_to_date(`date`, '%m/%d/%Y')
from supermarket_sales_dup;

Update supermarket_sales_dup
Set `date` = str_to_date(`date`, '%m/%d/%Y');

Alter table supermarket_sales_dup
modify `date` date; 

Select*
from supermarket_sales_dup; 

Alter table supermarket_sales_dup
add column time_of_day varchar (15);

Select time, 
( case 
When time between '00:00' and '11:59' then 'Morning'
When time between '12:00' and '17:59' then 'afternoon'
else 'evening'
 end) as time_of_day
 from supermarket_sales_dup;
 
 Update supermarket_sales_dup
 set time_of_day = ( case 
When time between '00:00' and '11:59' then 'Morning'
When time between '12:00' and '17:59' then 'afternoon'
else 'evening'
 end); 
 
 Alter table supermarket_sales_dup
 Add column day_of_week varchar(15);
 
 Select dayname(date)
 from supermarket_sales_dup;
 
 Update supermarket_sales_dup
 Set day_of_week = dayname(date);
 
 Alter table supermarket_sales_dup
 Add column Month varchar(15);
 
 Select monthname(date)
 from supermarket_sales_dup;
 
 Update supermarket_sales_dup
 set month = monthname(date);
 
 Select*
 from supermarket_sales_dup;
 
 ----------------------------------- Data Explortation-----------------------------------

----------- What is the total Customers the data have?-----------

Select 
count(distinct Invoice_ID) as total_customers
from 
supermarket_sales_dup
;

----------- How many unique Cities, Branch does the data have?-----------
Select 
distinct city, branch
from 
supermarket_sales_dup; 

----------- How many unique Customer type does the data have?-----------

Select 
distinct customer_type
from 
supermarket_sales_dup; 

----------- How many unique Product line does the data have?-----------

Select 
distinct Product_line
from 
supermarket_sales_dup;

----------- What is the most common payment method?-----------

SELECT 
DISTINCT payment, 
COUNT(*) AS common_method
FROM
supermarket_sales_dup
GROUP BY payment
ORDER BY common_method DESC;


 ----------------------------------- Business Key Problems and Answers --------------------


----------------- Q1.What is the most selling product line -----------------------------

SELECT DISTINCT
Product_line, sum(Quantity) AS Highest_product
FROM
supermarket_sales_dup
GROUP BY Product_line
ORDER BY Highest_product DESC;

----------------- Q2.What is the total revenue by month ------------------------

SELECT DISTINCT
Month, round(sum(total),2) AS Highest_revenue
FROM
supermarket_sales_dup
GROUP BY Month
ORDER BY Highest_revenue DESC;

----------------- Q3.What month had the largest COGS  ---------------------

SELECT DISTINCT
Month, sum(cogs) AS Rank_cogs
FROM
supermarket_sales_dup
GROUP BY Month
ORDER BY rank_cogs DESC;

-------------------- Q4.What product line had the largest revenue  ------------------

SELECT DISTINCT
Product_line, round(Sum(Total),2) AS ranked_revenue_generated
FROM
supermarket_sales_dup
GROUP BY Product_line
ORDER BY ranked_revenue_generated DESC;

----------------- Q5.What is the sale at '2019-03-25' --------------------

Select *
from 
supermarket_sales_dup
where 
`date` = '2019-03-25';

----------- Q6.Retrive all transactions where product line is Electronic accessories and quantity is more than 5 in March -------------

 Select*
 from 
 supermarket_sales_dup
 where product_line = 'Electronic accessories'
 and quantity > 5
 having month = 'March';

-------------- Q7.Retrive all transactions where total sale is greater than 700 -------------

Select *
from supermarket_sales_dup
where total > 700;

----------------- Q8.What is the total transactions made by each gender in each product line -------------
Select
Product_line, gender,
count(*) AS total_transaction
FROM
supermarket_sales_dup
GROUP BY Product_line,gender
ORDER BY 1;

------------- -------- Q9.What is the average sale for each month and the best selling month in the year -------------
Select month,
round(Avg(total),2) as Avg_sale,
Round(sum(total),2) as Best_sale_month
from supermarket_sales_dup
group by month
order by Avg_sale, Best_sale_month desc;

----------- Q10.Find the top 5 customers with the highest total sale -------------


Select invoice_id , 
sum(total) as Highest_purshase
from supermarket_sales_dup
group by invoice_id , total
order by Highest_purshase desc
limit 5;

-------------- Q11.Find the distinct customers who purchased from each product line -------------

Select
distinct Product_line,
count(Invoice_ID)as Num_Purchased
from supermarket_sales_dup
group by 
 Product_line
order by Num_Purchased;

Select *
from supermarket_sales_dup;

-------------- Q12.what is the total sales in each time of the day -------------

Select 
distinct 
time_of_day,
round(sum(total),2) 
as Total_sale
from 
supermarket_sales_dup
group by 
time_of_day
order by 
Total_sale desc;

```
