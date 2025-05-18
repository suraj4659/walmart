# walmart

### Q2: Highest-Rated Category in Each Branch
 ---- Display the branch, category, and avg rating
```
SELECT branch, category, avgr, rank 
FROM (
  SELECT branch, category, AVG(rating) AS avgr,
         RANK() OVER(PARTITION BY branch ORDER BY AVG(rating) DESC) AS rank
  FROM sales_data
  GROUP BY 1, 2
) AS ranked
WHERE rank = 1; ```

\-- Q3: Identify the busiest day for each branch based on the number of transactions
select \* from (select distinct(branch),date,count(*) ,
rank() over(partition by branch order by count(*) desc) as rank
from sales\_data
group by 1,2
order by 1
)
where rank = 1;

SELECT
branch,
date as day\_name,
COUNT(\*) AS no\_transactions
FROM sales\_data
GROUP BY branch, day\_name

\-- Q4: Calculate the total quantity of items sold per payment method
select payment\_method,count(\*),sum(quantity) from sales\_data
group by payment\_method

\-- Q5: Determine the average, minimum, and maximum rating of categories for each city
select \* from sales\_data

select city , category,avg(rating),min(rating),max(rating) from sales\_data
group by 1,2

\-- Q6: Calculate the total profit for each category
select category,sum(total\_sales),
sum( total\_sales \* profit\_margin)from sales\_data
group by 1

\-- Q7: Determine the most common payment method for each branch

with cte as (select distinct(branch),payment\_method,count(*),
rank() over(partition by branch order by count(*) desc ) as rank from sales\_data
group by 1,2)
select branch,payment\_method,rank from cte
where rank = 1

\-- Q8: Categorize sales into Morning, Afternoon, and Evening shifts

select branch,
case when hour(time) < 12 then 'morning'
when HOUR(time) < between 12 and 17 then 'afternoon'
else 'evening '

end as shift,
count(\*)
from sales\_data
group by 1,2

```
  SELECT
branch,
CASE 
    WHEN extract (HOUR from (TIME::time)) < 12 THEN 'Morning'
    WHEN extract (HOUR from (TIME::time)) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
END AS shift,
COUNT(*) AS num_invoices
```

FROM sales\_data
GROUP BY branch, shift
ORDER BY branch, num\_invoices DESC;

* Q9: Identify the 5 branches with the highest revenue decrease ratio from last year to current year (e.g., 2022 to 2023)

with revenue\_2022 as (SELECT
branch,
SUM(total\_sales) AS revenue
FROM
sales\_data
WHERE
EXTRACT(YEAR FROM date :: date) = 2022
GROUP BY
branch),

revenue\_2023 as (SELECT
branch,
SUM(total\_sales) AS revenue
FROM
sales\_data
WHERE
EXTRACT(YEAR FROM date :: date) = 2023
GROUP BY
branch)

```
select r2.branch,
r2.revenue as rn2,
r3.revenue as rn3,
round((r2.revenue-r3.revenue)::numeric/r2.revenue::numeric * 100) as decraeseing_revenue
from 
```

revenue\_2022 as r2
join
revenue\_2023 as r3
on r2.branch=r3.branch
where r2.revenue > r3.revenue
order by 4 desc limit 5 can you make  a readme for goyhub from this all i ahve provdie you
