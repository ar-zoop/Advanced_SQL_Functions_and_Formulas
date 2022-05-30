# Question 1
<img src="image1.png">

```
use upgrad;
select quantityInStock, rank() over ( order by quantityInStock desc) as quantityRank from products;
```

# Question 2
<img src="imag2.png">

```
use upgrad;
with temp_table as(
select orderNumber, (priceEach*quantityOrdered) as indiviualOrderAmount
from Orderdetails
order by orderNumber, indiviualOrderAmount desc
)
select *,
sum(indiviualOrderAmount) over (partition by orderNumber order by orderNumber)
from temp_table;
```

# Question 3
<img src="image3.png">

```
select *,
rank() over w as Rank,
dense_rank() over w as Dense Rank,
percent_rank() over w as Percent Rank
from shipping_dimen
window w as (partition by Ship_Mode order by count(*));
```

# Question 4
<img src="image4.png">

```
use upgrad;
select name,salary,
case
    when salary <=2.5 then 'A'
    when salary> 2.5 and salary<=5 then 'B'
    when salary> 5 and salary <=10 then 'C'
    else 'D'
end as 'Tax Slab'
from Salaries;
```

