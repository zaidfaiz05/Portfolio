1. What are the standard ingredients for each pizza?
```sql
select pt.topping_name --count(distinct pizza_id)
from pizza_recipes pr
left join lateral 
unnest(string_to_array(pr.toppings, ', ')) as topping on true
join pizza_toppings pt on pt.topping_id=topping::integer
group by pt.topping_name
having count(distinct pizza_id) = 2 ;
```


2. What was the most commonly added extra?
```sql
select topping_name from customer_orders co
left join lateral
unnest(string_to_array(extras,', ')) as extra on true
join pizza_toppings pt on pt.topping_id=extra::INTEGER
where length(extra) > 0
and extra <> 'null'
group by topping_name
order by count(distinct pizza_id) desc
limit 1;
```

3. What was the most common exclusion?
```sql
select topping_name from customer_orders co
left join lateral
unnest(string_to_array(exclusions,', ')) as exclusion on true
join pizza_toppings pt on pt.topping_id=exclusion::integer
where exclusion <> 'null'
group by topping_name
order by count(distinct pizza_id)
limit 1;
```

4. Generate an order item for each record in the customers_orders table in the format of one of the following:
-- Meat Lovers
-- Meat Lovers - Exclude Beef
-- Meat Lovers - Extra Bacon
-- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

```sql
with extras AS (
select
co.order_id,
co.pizza_id,
co.extras,
string_agg(distinct pt.topping_name, ', ') as added_xtra
from customer_orders co
left join lateral 
unnest(string_to_array(co.extras, ', ')) as extra on true
join pizza_toppings pt on pt.topping_id = extra::integer
where length(extra) > 0
nd extra <> 'null'
group by co.order_id, co.pizza_id, co.extras
order by co.order_id desc
),
excluded as (
select
co.order_id,
co.pizza_id,
co.exclusions,
string_agg(distinct pt.topping_name, ', ') as excluded
from customer_orders co
left join lateral 
unnest(string_to_array(co.exclusions, ', ')) as extra on true
join pizza_toppings pt on pt.topping_id = extra::integer
where length(extra) > 0
and extra <> 'null'
group by co.order_id, co.pizza_id, co.exclusions
order by co.order_id DESC
)
select
co.order_id,
Concat(case when pn.pizza_name='Meatlovers' then 'Meat Lovers' else pn.pizza_name END,
coalesce(' - Extra ' || ext.added_xtra,''),
coalesce(' - Exclude ' ||exc.excluded,'')) as order_details
from customer_orders co
left join extras ext om ext.order_id = co.order_id and ext.pizza_id = co.pizza_id and ext.extras = co.extras
left join excluded exc on exc.order_id = co.order_id AND exc.pizza_id = co.pizza_id and exc.exclusions = co.exclusions
join pizza_names pn on pn.pizza_id=co.pizza_id
```

5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
-- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
```sql
with exclusions as (
select 
co.order_id,
co.pizza_id,
S.value::integer as topping_id
from customer_orders as co
left join lateral unnest(string_to_array(co.exclusions, ', ')) as S(value) on true
where length(S.value) > 0 and S.value <> 'null'
),
extras as (
select 
co.order_id,
co.pizza_id,
S.value::integer as topping_id,
pt.topping_name
from customer_orders as co
left join lateral unnest(string_to_array(co.extras, ', ')) as S(value) on true
inner join pizza_toppings as pt on pt.topping_id = S.value::integer
where length(S.value) > 0 and S.value <> 'null'
),
orders as (
select distinct
co.order_id,
co.pizza_id,
S.value::integer as topping_id,
pt.topping_name
from customer_orders as co
inner join pizza_recipes as pr on co.pizza_id = pr.pizza_id
left join lateral unnest(string_to_array(pr.toppings, ', ')) as S(value) on true
inner join pizza_toppings as pt on pt.topping_id = S.value::integer
),
orders_with_extras_and_exclusions as (
select 
o.order_id,
o.pizza_id,
o.topping_id,
o.topping_name
from orders as o
left join exclusions as exc on exc.order_id = o.order_id and exc.pizza_id = o.pizza_id and exc.topping_id = o.topping_id
where exc.topping_id is null

union all 

select 
e.order_id,
e.pizza_id,
e.topping_id,
e.topping_name
from extras as e
),
topping_count as (
select 
o.order_id,
o.pizza_id,
o.topping_name,
count(*) as n
from orders_with_extras_and_exclusions as o
group by 
o.order_id,
o.pizza_id,
o.topping_name
)
select 
order_id,
pizza_id,
string_agg(
case when n > 1 then n || 'x' || topping_name
else topping_name
end, ', '
    ) s ingredient
from TOPPING_COUNT
group by order_id, pizza_id;
```

6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
```sql
with exclusions as (
select 
co.order_id,
co.pizza_id,
S.value::INTEGER as topping_id
from, customer_orders  co
left join lateral unnest(string_to_array(co.exclusions, ', ')) as S(value) on true
where length(S.value) > 0 and S.value <> 'null'
),
extras as (
select
co.order_id,
co.pizza_id,
S.value::INTEGER AS topping_id,
pt.topping_name
from customer_orders AS co
left join lateral unnest(string_to_array(co.extras, ', ')) as S(value) on true
inner join pizza_toppings  pt on pt.topping_id = S.value::INTEGER
where length(S.value) > 0 and S.value <> 'null'
),
orders as (
select distinct
co.order_id,
co.pizza_id,
S.value::INTEGER as topping_id,
pt.topping_name
from customer_orders  co
inner join pizza_recipes  pr on co.pizza_id = pr.pizza_id
left join lateral unnest(string_to_array(pr.toppings, ', ')) AS S(value) on true
inner join pizza_toppings pt on pt.topping_id = S.value::INTEGER
),
orders_with_extras_exclusions as (
select 
o.order_id,
o.pizza_id,
o.topping_id,
o.topping_name
from orders o
left join exclusions exc on exc.order_id = o.order_id and exc.pizza_id = o.pizza_id and exc.topping_id = o.topping_id
where exc.topping_id IS NULL

union all

select 
 e.order_id,
 e.pizza_id,
 e.topping_id,
 e.topping_name
 from extras as e
)

select 
topping_name,
count(topping_id)
from orders_with_extras_exclusions o
inner join runner_orders as ro on o.order_id = ro.order_id
where cancellation is null
or cancellation = 'null'
or cancellation= ''
group by topping_name
order by count(topping_id) desc
```
