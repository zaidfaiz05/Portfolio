1. What is the total amount each customer spent at the restaurant? - Customer A is the top customer with total spending of 76

```sql 
select s.customer_id, sum(m.price) from sales s
join menu m on s.product_id=m.product_id
group by s.customer_id
order by sum(m.price) desc; 

```

2. How many days has each customer visited the restaurant?

```sql  
select customer_id, count(distinct order_date) from sales
group by customer_id; 

```


3. What was the first item from the menu purchased by each customer?

```sql 
select distinct customer_id, min(order_date) as first_order, product_name from sales s
join menu m on s.product_id=m.product_id
where order_date = (select min(order_date) from sales
                    where product_id=s.product_id)
group by customer_id, product_name;
``` 

4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql 
select product_name, count(m.product_id) from sales s
join menu m on s.product_id=m.product_id
group by product_name
order by count(m.product_id) desc
limit 1;
```

5. Which item was the most popular for each customer?
```sql 
select customer_id,product_name, count(m.product_id) from sales s
join menu m on s.product_id=m.product_id
group by customer_id,product_name
order by customer_id,count(m.product_id) desc;
```

6. Which item was purchased first by the customer after they became a member?
```sql 
select s.customer_id, min(order_date), product_name from sales s
join members mm on mm.customer_id=s.customer_id
join menu m on m.product_id=s.product_id
where order_date >= join_date
group by s.customer_id, m.product_name;


SELECT 
    s.customer_id,
    mn.product_name AS first_item_purchased,
    s.order_date AS first_order_date
FROM sales s
JOIN members m ON s.customer_id = m.customer_id
JOIN menu mn ON s.product_id = mn.product_id
WHERE s.order_date >= m.join_date
  AND s.order_date = (
      SELECT MIN(order_date)
      FROM sales
      WHERE customer_id = s.customer_id
        AND order_date >= m.join_date
  );

```


7. Which item was purchased just before the customer became a member?

```sql 
select s.customer_id, min(order_date), product_name from sales s
join members mm on mm.customer_id=s.customer_id
join menu m on m.product_id=s.product_id
where order_date < join_date
group by s.customer_id, m.product_name;

SELECT 
    s.customer_id,
    mn.product_name AS item_purchased_before_membership,
    s.order_date AS purchase_date
FROM sales s
JOIN members m ON s.customer_id = m.customer_id
JOIN menu mn ON s.product_id = mn.product_id
WHERE s.order_date < m.join_date
  AND s.order_date = (
      SELECT MAX(order_date)
      FROM sales
      WHERE customer_id = s.customer_id
        AND order_date < m.join_date
  );

```

8. What is the total items and amount spent for each member before they became a member?

```sql 
select s.customer_id, count(product_name), sum(price) from sales s
join members mm on mm.customer_id=s.customer_id
join menu m on m.product_id=s.product_id
where order_date < join_date
group by s.customer_id;
```


9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql 
select customer_id, sum(case when product_name='sushi' then price*20 else price*10 end) as total_point from sales s 
join menu m on s.product_id=m.product_id
group by customer_id
order by customer_id;
```

10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at 
the end of January?
```sql 
WITH points_calculation AS (
    SELECT 
        s.customer_id,
        s.order_date,
        mn.product_name,
        mn.price,
        CASE 
            WHEN s.order_date BETWEEN m.join_date AND m.join_date + INTERVAL '6 days' THEN mn.price * 2
            ELSE mn.price
        END AS points_earned
    FROM sales s
    JOIN members m ON s.customer_id = m.customer_id
    JOIN menu mn ON s.product_id = mn.product_id
    WHERE s.customer_id IN ('A', 'B')
      AND s.order_date <= '2021-01-31'
)
SELECT 
    customer_id,
    SUM(points_earned) AS total_points
FROM points_calculation
GROUP BY customer_id;

SELECT 
    s.customer_id, 
    SUM(
        CASE 
            WHEN order_date BETWEEN mm.join_date AND DATE_ADD(mm.join_date, INTERVAL 6 DAY) THEN m.price * 2 --(mm.join_date + INTERVAL '6' DAY)
            ELSE m.price
        END
    ) AS total_points
FROM sales s
JOIN menu m ON s.product_id = m.product_id
JOIN members mm ON s.customer_id = mm.customer_id
WHERE MONTH(order_date) = 1 AND YEAR(order_date) = 2021
GROUP BY s.customer_id;
```
