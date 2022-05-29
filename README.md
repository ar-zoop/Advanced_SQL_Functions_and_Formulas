Below are all the concepts with queries I learnt during my "Advanced SQL: Learn SQL Functions and Formulas" from upGrad.

Certificate link: 

# Window Functions

## The Rank Function 
example-
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
question-
<img src="image1.png">
```
use upgrad;
select quantityInStock, rank() over ( order by quantityInStock desc) as quantityRank from products;
```
