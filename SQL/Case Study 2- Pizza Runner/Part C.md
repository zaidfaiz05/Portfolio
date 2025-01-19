1. What are the standard ingredients for each pizza?
```sql
SELECT pt.topping_name --count(distinct pizza_id)
FROM pizza_recipes pr
LEFT JOIN LATERAL 
UNNEST(STRING_TO_ARRAY(pr.toppings, ', ')) AS topping ON TRUE
JOIN pizza_toppings pt on pt.topping_id=topping::INTEGER
GROUP BY pt.topping_name
HAVING count(distinct pizza_id) = 2 ;
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
order by count(distinct pizza_id) DESC
limit 1;
```

3. What was the most common exclusion?
```sql
select topping_name from customer_orders co
left join lateral
unnest(string_to_array(exclusions,', ')) as exclusion on true
join pizza_toppings pt on pt.topping_id=exclusion::INTEGER
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
WITH EXTRAS AS (
    SELECT
        co.order_id,
        co.pizza_id,
        co.extras,
        STRING_AGG(DISTINCT pt.topping_name, ', ') AS added_xtra
    FROM customer_orders co
    LEFT JOIN LATERAL 
        UNNEST(STRING_TO_ARRAY(co.extras, ', ')) AS extra ON TRUE
    JOIN pizza_toppings pt ON pt.topping_id = extra::INTEGER
    WHERE LENGTH(extra) > 0
      AND extra <> 'null'
    GROUP BY co.order_id, co.pizza_id, co.extras
    ORDER BY co.order_id DESC
),
EXCLUDED AS (
    SELECT
        co.order_id,
        co.pizza_id,
        co.exclusions,
        STRING_AGG(DISTINCT pt.topping_name, ', ') AS excluded
    FROM customer_orders co
    LEFT JOIN LATERAL 
        UNNEST(STRING_TO_ARRAY(co.exclusions, ', ')) AS extra ON TRUE
    JOIN pizza_toppings pt ON pt.topping_id = extra::INTEGER
    WHERE LENGTH(extra) > 0
      AND extra <> 'null'
    GROUP BY co.order_id, co.pizza_id, co.exclusions
    ORDER BY co.order_id DESC
)
SELECT
    co.order_id,
    CONCAT(CASE WHEN pn.pizza_name='Meatlovers' THEN 'Meat Lovers' ELSE pn.pizza_name END,
    COALESCE(' - Extra ' || ext.added_xtra,''),
    COALESCE(' - Exclude ' ||exc.excluded,'')) as order_details
FROM customer_orders co
LEFT JOIN EXTRAS ext ON ext.order_id = co.order_id AND ext.pizza_id = co.pizza_id AND ext.extras = co.extras
LEFT JOIN EXCLUDED exc ON exc.order_id = co.order_id AND exc.pizza_id = co.pizza_id AND exc.exclusions = co.exclusions
JOIN pizza_names pn on pn.pizza_id=co.pizza_id
```

5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
-- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
```sql
WITH EXCLUSIONS AS (
    SELECT 
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id
    FROM customer_orders AS co
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(co.exclusions, ', ')) AS S(value) ON TRUE
    WHERE LENGTH(S.value) > 0 AND S.value <> 'null'
),
EXTRAS AS (
    SELECT 
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id,
        pt.topping_name
    FROM customer_orders AS co
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(co.extras, ', ')) AS S(value) ON TRUE
    INNER JOIN pizza_toppings AS pt ON pt.topping_id = S.value::INTEGER
    WHERE LENGTH(S.value) > 0 AND S.value <> 'null'
),
ORDERS AS (
    SELECT DISTINCT
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id,
        pt.topping_name
    FROM customer_orders AS co
    INNER JOIN pizza_recipes AS pr ON co.pizza_id = pr.pizza_id
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(pr.toppings, ', ')) AS S(value) ON TRUE
    INNER JOIN pizza_toppings AS pt ON pt.topping_id = S.value::INTEGER
),
ORDERS_WITH_EXTRAS_AND_EXCLUSIONS AS (
    SELECT 
        o.order_id,
        o.pizza_id,
        o.topping_id,
        o.topping_name
    FROM ORDERS AS o
    LEFT JOIN EXCLUSIONS AS exc ON exc.order_id = o.order_id AND exc.pizza_id = o.pizza_id AND exc.topping_id = o.topping_id
    WHERE exc.topping_id IS NULL

    UNION ALL 

    SELECT 
        e.order_id,
        e.pizza_id,
        e.topping_id,
        e.topping_name
    FROM EXTRAS AS e
),
TOPPING_COUNT AS (
    SELECT 
        o.order_id,
        o.pizza_id,
        o.topping_name,
        COUNT(*) AS n
    FROM ORDERS_WITH_EXTRAS_AND_EXCLUSIONS AS o
    GROUP BY 
        o.order_id,
        o.pizza_id,
        o.topping_name
)
SELECT 
    order_id,
    pizza_id,
    STRING_AGG(
        CASE
            WHEN n > 1 THEN n || 'x' || topping_name
            ELSE topping_name
        END, ', '
    ) AS ingredient
FROM TOPPING_COUNT
GROUP BY order_id, pizza_id;
```

6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
```sql
WITH EXCLUSIONS AS (
    SELECT 
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id
    FROM customer_orders AS co
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(co.exclusions, ', ')) AS S(value) ON TRUE
    WHERE LENGTH(S.value) > 0 AND S.value <> 'null'
),
EXTRAS AS (
    SELECT 
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id,
        pt.topping_name
    FROM customer_orders AS co
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(co.extras, ', ')) AS S(value) ON TRUE
    INNER JOIN pizza_toppings AS pt ON pt.topping_id = S.value::INTEGER
    WHERE LENGTH(S.value) > 0 AND S.value <> 'null'
),
ORDERS AS (
    SELECT DISTINCT
        co.order_id,
        co.pizza_id,
        S.value::INTEGER AS topping_id,
        pt.topping_name
    FROM customer_orders AS co
    INNER JOIN pizza_recipes AS pr ON co.pizza_id = pr.pizza_id
    LEFT JOIN LATERAL UNNEST(STRING_TO_ARRAY(pr.toppings, ', ')) AS S(value) ON TRUE
    INNER JOIN pizza_toppings AS pt ON pt.topping_id = S.value::INTEGER
),
ORDERS_WITH_EXTRAS_AND_EXCLUSIONS AS (
    SELECT 
        o.order_id,
        o.pizza_id,
        o.topping_id,
        o.topping_name
    FROM ORDERS AS o
    LEFT JOIN EXCLUSIONS AS exc ON exc.order_id = o.order_id AND exc.pizza_id = o.pizza_id AND exc.topping_id = o.topping_id
    WHERE exc.topping_id IS NULL

    UNION ALL 

    SELECT 
        e.order_id,
        e.pizza_id,
        e.topping_id,
        e.topping_name
    FROM EXTRAS AS e
)

Select 
topping_name,
count(topping_id)
from ORDERS_WITH_EXTRAS_AND_EXCLUSIONS o
INNER JOIN runner_orders as ro on o.order_id = ro.order_id
where cancellation is null
or cancellation = 'null'
or cancellation= ''
group by topping_name
order by count(topping_id) desc
```
