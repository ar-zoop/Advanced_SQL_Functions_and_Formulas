# Window Functions
## ROW_NUMBER()
It assigns a sequential number to each row in the result set. The first number begins with one.
Practiced on sample database:
```
select row_number() over (order by wind_speed) as rowNum, station_number, year
from station_data
group by year;
```
```
select row_number() over (
partition by wind_speed
order by year
) rowNum, station_number, year,wind_speed
from station_data
group by year;
```