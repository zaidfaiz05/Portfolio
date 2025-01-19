1. How many pizzas were ordered?
```sql
select count(*) as pizza_ordered 
from customer_orders;
```

2. How many unique customer orders were made?
```sql
select count(distinct order_id) as unique_order 
from customer_orders;
```

3. How many successful orders were delivered by each runner?
```sql
select runner_id,count(pickup_time) as total_delivery_count
from runner_orders 
where pickup_time <>'null'
group by runner_id
order by runner_id;
```

4. How many of each type of pizza was delivered?
```sql
select pizza_id, count(*) as pizza_delivered from customer_orders co
join runner_orders ro on co.order_id = ro.order_id 
where pickup_time <> 'null'
group by pizza_id
order by pizza_id;
```

5. How many Vegetarian and Meatlovers were ordered by each 
customer?
```sql
select customer_id, count(co.pizza_id) as pizza_ordered, pizza_name from customer_orders co 
join pizza_names pn on co.pizza_id= pn.pizza_id
group by customer_id,pizza_name
order by customer_id;
```

6. What was the maximum number of pizzas delivered in a single order?
```sql
select co.order_id, count(pizza_id) from runner_orders ro 
join customer_orders co on ro.order_id=co.order_id
where pickup_time <>'null'
group by co.order_id
order by count(pizza_id) desc
limit 1;
```

7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
select customer_id,  
sum(case when ((exclusions is not null and exclusions <> 'null' and length(exclusions)>0)
and (extras is not null and extras <> 'null' and length(extras)>0))=TRUE
then 1
else 0
end) as changes,
sum(case when ((exclusions is not null and exclusions <> 'null' and length(exclusions)>0)
and (extras is not null and extras <> 'null' and length(extras)>0))=TRUE
then 0
else 1
end) as no_changes,
count(pizza_id)
from customer_orders co join runner_orders ro 
on co.order_id=ro.order_id
where pickup_time <>'null'
group by customer_id;
```

8. How many pizzas were delivered that had both exclusions and extras?
```sql
select count(pizza_id) from customer_orders co
join runner_orders ro 
on co.order_id=ro.order_id
where pickup_time <>'null'
and (exclusions IS NOT NULL AND exclusions <> 'null' AND LENGTH(exclusions)>0)
AND (extras IS NOT NULL AND extras <> 'null' AND LENGTH(extras)>0);
```

9. What was the total volume of pizzas ordered for each hour of the day?
```sql
select extract(hour from order_time) as hour, --DATE_PART('hour', order_time)
count(pizza_id) 
from customer_orders
group by extract(hour from order_time)
;
```

10. What was the volume of orders for each day of the week?
```sql
select extract(dow from order_time) as day_num,--DATE_PART('dow', order_time)
count(pizza_id) 
from customer_orders
group by extract(dow from order_time)
;
```
