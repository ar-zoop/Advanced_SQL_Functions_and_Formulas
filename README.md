Below are all the concepts with in course queries I learnt during my "Advanced SQL: Learn SQL Functions and Formulas" from upGrad.
asdfghjhgfxc
Check out the other .md files for the rest of the content. 

Certificate link: 

# Window Functions

## The Rank Function 
The following snippet orders the sales by rank:

```
select ord_id, round(sales) as rounded sales, customer name, rank() over (order by sales desc) as sale_amount_rank
from market_fact_full
inner join cust_dimen
using (cust_id)
where customer_name like 'Aron Smayling';
```

The following snippet 
1) renames the above query's result to Rank_Info and 
2) gets us top-n results by treating it as a separate table column:
```
with Rank_Info as(
    select ord_id, round(sales) as rounded sales, customer name, rank() over (order by sales desc) as sale_amount_rank
    from market_fact_full
    inner join cust_dimen
    using (cust_id)
    where customer_name like 'Aron Smayling';
)
select ord  select ord_id, round(sales) as rounded sales, customer name, sale_amount_rank
from Rank_Info
where sale_amount_rank<=10;
```

## Rank vs Dense Rank
The following snippet displays the difference in rank() and dense_rank() functions:
```
select ord_id, round(sales) as rounded sales, customer name, rank() over (order by discount desc) as discount_rank, dense_rank over (order by discount desc) as discount_dense_rank
from market_fact_full
inner join cust_dimen
using (cust_id)
where customer_name like 'Aron Smayling';
```

## Rank on Aggregate operations
The following snippet displays customers in the decreasing order of the number of orders placed and their ranks:
```
select customer_name, count(ord_id) 'ord_id',
rank() over (order by count(ord_id) desc ) 'order_count_rank'
from cust_dimen
inner join market_fact_full
using (cust_id)
group by customer_name;
```

## Partitioning
The following snippet
1) renames a query to ship_table 
2) creates rank over number_shipments but resets every time the ship_mode changes. Therefore grouped ranking.
```
with ship_table as (
    select ship_mode, month(ship_date) 'shipment_month', count(*) as number_shipment
    from shipping_dimen
    group by month(ship_date), ship_mode
    order by ship_mode, month(ship_date)
)

select * ,
rank() over (partition by ship_mode order by number_shipments desc) as shipment_mode
from ship_table;
```

## Named Windows
The following snippet uses the same condition named as 'w' for various window functions:
```
select ord_id, discount, customer_name,
row_number() over w,
rank() over w,
dense_rank() over w
from market_fact_full
inner join cut_dimen
using(cust_id)
where customer_name='Aaron Smayling'
window w as (order by Discount desc);
```

# Query Optimization
## Indexing
It is created on the columns that are frequently used in your where clause.

Index statement:
```
create index
```

## Join vs Nested Queries
As long as the selection is being performed on indexes, both are good. The query processor can convert a join into a nested query and vice versa for optimized performance. This isn't our task to worry about.

# Programming Constructs and Stored Functions

## Case Statement 
The following snippet shows an example for using case statement. It is basically the if then else clause of SQL. It will display a column and show the respective aliases under respective conditions:

```
select Market_fact_id, profit
case (
    when profit< -500 then 'Huge loss'
    when profit between -500 to 0 then 'Bearable loss'
    when profit between 0 to 500 then 'Decent profit'
    else 'Huge profit'
end) as market_fact_report
from market_fact_full;
```
The following snippet shows a complicated version of a case statement. Top 20% of customers get gold, next 35% get silver, the rest get bronze.
```
with raw_file as(
    with raw_data as(
        select cust_id, customer_name, round(sum(sales)) as total_sales
        from market_fact_full
        inner join cust_dimen
        using(cust_id)
        group by cust_id
        order by total_sales desc
    )
    select *,
    percent_rank() over (order by total_sales desc) as 'rank1'
    from raw_data
)
select *,
(case 
    when rank1<0.2 then 'Gold'
    when rank1 between 0.2 and 0.55 then 'Silver'
    else 'Bronze'
end) as customer_type
from raw_file;
```

## Stored Functions

The following snippets demonstrates the use of functions in sql:
```
delimiter $$

create function productStatus(profit int)
returns varchar (25) deterministic

begin
    declare market_fact_report varchar (25);
    if profit<-500 then
        set market_fact_report='huge loss';
    elseif(profit>=-500 and profit < 0) then
        set market_fact_report='bearable loss';
    elseif(profit>=0 and profit<500) then
        set market_fact_report='decent profit';
    else
        set market_fact_report='great profit';
    end if;
    return market_fact_report;
end; $$

delimiter ;

select market_fact_id, profit, productstatus(profit) as market_fact_report
from market_fact_full;
```
Meaning of keywords used in the above code:
1. deterministic= the function returns the same value for the same input
2. delimiter '$$' 
 = the default delimiter is ';' and since ';' is being used in the code we cannot use ';' as the delimiter for sql. hence we change the delimiter to '$$'.
4. declare= creates a variable
5. set= stores a value in the variable
