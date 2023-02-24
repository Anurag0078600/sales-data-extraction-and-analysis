# sales-data-extraction-and-analysis
Sales data analysis using mysql
-- How many registered clients we have
select count(*) from customers; --38

-- Where all our team provide services
select markets_code, markets_name, zone from markets;
select count(markets_code) from markets;--17

-- If we ever served paris and New York.
select * from transactions where market_code = 'Mark097' or market_code = 'Mark999'; -- 0

-- Checking the total amount earned by each market place.
select sum(sales_amount) as total_sales, markets_name from transactions t join markets m on t.market_code = m.markets_code
group by markets_name with rollup;  -- rollup gives grand total of the column (986565766)

select distinct year(order_date) from transactions;-- 2017 to 2020


-- creating a procedure to view monthly commulative data of sale in a year.
delimiter //
create procedure revenue_in_year(in v_order_date year)
begin
select sum(sales_amount) as sales_amt, date_format(order_date,'%M') as month_date from (select *,year(order_date) as year_date from transactions where year(order_date)=v_order_date) a
group by month_date
order by date_format(month_date,'%m') ;
end //
delimiter ;

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
