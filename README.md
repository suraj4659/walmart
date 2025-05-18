# walmart

### Q2: Highest-Rated Category in Each Branch
 ---- Display the branch, category, and avg rating
sql
SELECT branch, category, avgr, rank 
FROM (
  SELECT branch, category, AVG(rating) AS avgr,
         RANK() OVER(PARTITION BY branch ORDER BY AVG(rating) DESC) AS rank
  FROM sales_data
  GROUP BY 1, 2
) AS ranked
WHERE rank = 1; 

 ### Q3: Identify the busiest day for each branch based on the number of transactions
''' SELECT * 
FROM (
  SELECT DISTINCT(branch), date, COUNT(*),
         RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) AS rank
  FROM sales_data
  GROUP BY 1, 2
  ORDER BY 1
)
WHERE rank = 1;
'''

Q4: Calculate the total quantity of items sold per payment method
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
WITH revenue_2022 AS (
  SELECT branch, SUM(total_sales) AS revenue
  FROM sales_data
  WHERE EXTRACT(YEAR FROM date::date) = 2022
  GROUP BY branch
),
revenue_2023 AS (
  SELECT branch, SUM(total_sales) AS revenue
  FROM sales_data
  WHERE EXTRACT(YEAR FROM date::date) = 2023
  GROUP BY branch
)
SELECT r2.branch,
       r2.revenue AS rn2,
       r3.revenue AS rn3,
       ROUND((r2.revenue - r3.revenue)::numeric / r2.revenue::numeric * 100) AS decreasing_revenue
FROM revenue_2022 AS r2
JOIN revenue_2023 AS r3 ON r2.branch = r3.branch
WHERE r2.revenue > r3.revenue
ORDER BY decreasing_revenue DESC
LIMIT 5;
```
