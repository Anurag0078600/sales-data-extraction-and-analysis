# sales-data-extraction-and-analysis
Sales data analysis using mysql

I got the data from one of the practice projects uploaded by codebasic (@dhaval Patel) I thankyou for making this kind of repositories, it made the learning very convenient .

SALES DATA Insight....(2017 to 2020)

1. What I found that there are 4 lookup sheets and 1 measure sheet in this database.

2. I created the connection between the lookup sheet and measure sheet(transactions) in EER diagram .

3. I checked  how many registered clients we have --> there were 38 Registered clients that we have in the business.

-- How many registered clients we have
select count(*) from customers; --38

4. I checked where all our team provide services -->there are total 17 registered market locations for our business.

-- Where all our team provide services
select markets_code, markets_name, zone from markets;
select count(markets_code) from markets;--17

5. I checked if we ever served out of india , as there were 2 registered foreign locations in registered market places --> We had no transaction history for these locations (- Reached on the conclusion that those were registered incorrectly or the two USD entries those were made had been redirected towards the 'New York' those were done from Delhi NCR location, however no firm conclusion yet, need to put it as anomaly to further investigate if Sales team want to investigate it further).

-- If we ever served paris and New York.
select * from transactions where market_code = 'Mark097' or market_code = 'Mark999'; -- 0

6. Checked the total sales from each market place and total sales throughout the business history--> we have done in total 98 Cr+ business till 2020.

-- Checking the total amount earned by each market place.
select sum(sales_amount) as total_sales, markets_name from transactions t join markets m on t.market_code = m.markets_code
group by markets_name with rollup;  -- rollup gives grand total of the column (986565766)

select distinct year(order_date) from transactions;-- 2017 to 2020

7. To get the sales data for particular year I created stored procedure so that we can get the sales information for every year without writing query even in future, and creating stored procedure views and indexes are one of the helpful tool to reduce the execution cost.

-- creating a procedure to view monthly commulative data of sale in a year.
delimiter //
create procedure revenue_in_year(in v_order_date year)
begin
select sum(sales_amount) as sales_amt, date_format(order_date,'%M') as month_date from (select *,year(order_date) as year_date from transactions where year(order_date)=v_order_date) a
group by month_date
order by date_format(month_date,'%m') ;
end //
delimiter ;


8. To get the data for a particular month of the year created a function  and checked the total sales amount for month of jan in year 2020 and got the amt 2 Cr+.

-- creating a function to view monthly_revenue for a particular month
delimiter //
create function monthly_revenue(v_order_date year, v1_order_date varchar(12)) returns double
Deterministic NO SQL READS SQL DATA
begin
declare sales_amt double;
select sum(sales_amount) into sales_amt from transactions
where year(order_date) = v_order_date and monthname(order_date) = v1_order_date
group by year(order_date),date_format(order_date, '%M');
return sales_amt;
end //
delimiter ;


Final Analysis- Checked and found a yearly falling trend in the business till 2020.













