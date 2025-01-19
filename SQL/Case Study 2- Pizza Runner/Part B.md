1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
SELECT COUNT(*) AS runner_count, (DATE_TRUNC('week', registration_date) + INTERVAL '4 days')::DATE AS week
FROM runners
GROUP BY (DATE_TRUNC('week', registration_date) + INTERVAL '4 days')::DATE
ORDER BY  week;
```
 
2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
SELECT runner_id, ROUND(AVG(EXTRACT(EPOCH FROM (CAST(pickup_time AS TIMESTAMP)-order_time))/60)) AS avg_pickup_time_in_mins
FROM runner_orders ro
JOIN customer_orders co 
on ro.order_id=co.order_id
WHERE pickup_time <> 'null'
group by runner_id;
```

3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
with cte as(select co.order_id,count(pizza_id) as pizza_count,
-- ROUND(AVG(EXTRACT(EPOCH FROM (CAST(pickup_time AS TIMESTAMP)-order_time))/60)) AS avg_pickup_time_in_mins,
ROUND(MAX(EXTRACT(EPOCH FROM (CAST(pickup_time AS TIMESTAMP) - order_time))/60)) AS max_pickup_time_in_mins
from customer_orders co
join runner_orders ro 
on co.order_id=ro.order_id
WHERE pickup_time <> 'null'
group by co.order_id)

select pizza_count, avg(max_pickup_time_in_mins) from cte
group by pizza_count;
```

4. What was the average distance travelled for each customer?
```sql
select customer_id, round(avg(replace(distance,'km','')::numeric(3,1)),1) as avg_distance
from runner_orders ro
JOIN customer_orders co 
on ro.order_id=co.order_id
where distance <> 'null'
group by customer_id
order by customer_id;
```

5. What was the difference between the longest and shortest delivery times for all orders?
```sql
select max(regexp_replace(duration, '[^0-9]', '', 'g'))::int-min(regexp_replace(duration, '[^0-9]', '', 'g'))::int as difference_btw_longest_and_shortest_delivery
from runner_orders ro
where duration <> 'null';
```

6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
select runner_id,order_id, round(avg(replace(distance,'km','')::numeric(3,1)),1)/avg(regexp_replace(duration, '[^0-9]', '', 'g')::numeric(3,1)) as avg_speed
from runner_orders ro
where duration <> 'null'
group by runner_id,order_id
order by runner_id,order_id;
```

7. What is the successful delivery percentage for each runner?
```sql
select runner_id,(sum(case when pickup_time ='null' then 0 else 1 end)*100.0) / count(order_id) as successful_delivery_percentage
from runner_orders
group by runner_id
order by runner_id;
```
