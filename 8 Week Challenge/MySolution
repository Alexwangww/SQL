**#1. What is the total amount each customer spent at the restaurant?**

    select
    	s.customer_id,
    	sum(m.price) as total_spent
    from
    	dannys_diner.sales s left join dannys_diner.menu m on s.product_id = m.product_id
    group by
    	s.customer_id;
**ANSWER**

| customer_id | total_spent |
| ----------- | ----------- |
| B           | 74          |
| C           | 36          |
| A           | 76          |

---

**#2. How many days has each customer visited the restaurant?**

    select
    	customer_id,
    	count(distinct order_date) as days_visited
    from
    	dannys_diner.sales
    group by
    	customer_id;
**ANSWER**

| customer_id | days_visited |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

---

**#3. What was the first item from the menu purchased by each customer?**

    with rank_cte as
    	(
    		SELECT 
          		    customer_id, 
          		    product_name, 
          		    order_date, 
    		    DENSE_RANK() over(PARTITION BY customer_id order by order_date) as rnk
    		from 
          		    dannys_diner.sales s inner join dannys_diner.menu m on s.product_id = m.product_id
    		group by 
          		    customer_id, 
          		    product_name, 
          		    order_date
    	)
    SELECT 
    	customer_id, 
            product_name
    from 
    	rank_cte
    where 
    	rnk = 1;     
**ANSWER**

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

---

**#4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

    select
    	m. product_name,
    	count(s.product_id) as count
    from
    	dannys_diner.sales s left join dannys_diner.menu m on s.product_id = m.product_id
    group by
    	m.product_name
    order by count desc
    	limit 1;
**ANSWER**

| product_name | count |
| ------------ | ----- |
| ramen        | 8     |

---

**#5. Which item was the most popular for each customer?**

    select
        customer_id,
        product_name,
        order_count
    from (
        select
            s.customer_id,
            m.product_name,
            count(s.product_id) as order_count,
            dense_rank() over (partition by s.customer_id order by count(s.product_id) desc) as rk
        from
            dannys_diner.sales s
            left join dannys_diner.menu m on s.product_id = m.product_id
        group by
            s.customer_id, m.product_name
    ) as x
    where
        x.rk = 1;
**ANSWER**

| customer_id | product_name | order_count |
| ----------- | ------------ | ----------- |
| A           | ramen        | 3           |
| B           | ramen        | 2           |
| B           | curry        | 2           |
| B           | sushi        | 2           |
| C           | ramen        | 3           |

---

**#6. Which item was purchased first by the customer after they became a member?**

    WITH sales_cte AS (
        SELECT
            sales.customer_id,
            sales.order_date,
            menu.product_name,
            row_number() OVER (
                PARTITION BY sales.customer_id
                ORDER BY sales.order_date
            )   AS order_rank
        FROM dannys_diner.sales
        left join dannys_diner.menu
            on sales.product_id = menu.product_id
        inner join dannys_diner.members
            ON sales.customer_id = members.customer_id AND sales.order_date >= members.join_date
    ) 
    SELECT DISTINCT
        customer_id,
        order_date,
        product_name
    FROM sales_cte
    WHERE order_rank = 1;
**ANSWER**

| customer_id | order_date               | product_name |
| ----------- | ------------------------ | ------------ |
| A           | 2021-01-07T00:00:00.000Z | curry        |
| B           | 2021-01-11T00:00:00.000Z | sushi        |

---

**#7. Which item was purchased just before the customer became a member?**

    WITH sales_cte AS (
        SELECT
            sales.customer_id,
            sales.order_date,
            menu.product_name,
            dense_rank() OVER (
                PARTITION BY sales.customer_id
                ORDER BY sales.order_date desc
            )   AS order_rank
        FROM dannys_diner.sales
        left join dannys_diner.menu
            on sales.product_id = menu.product_id
        inner join dannys_diner.members
            ON sales.customer_id = members.customer_id AND sales.order_date < members.join_date
    ) 
    SELECT DISTINCT
        customer_id,
        order_date,
        product_name
    FROM sales_cte
    WHERE order_rank = 1;
**ANSWER**

| customer_id | order_date               | product_name |
| ----------- | ------------------------ | ------------ |
| A           | 2021-01-01T00:00:00.000Z | curry        |
| A           | 2021-01-01T00:00:00.000Z | sushi        |
| B           | 2021-01-04T00:00:00.000Z | sushi        |

---

**#8. What is the total items and amount spent for each member before they became a member?**

    select
    	s.customer_id,
    	count(s.product_id) as total_unit,
    	sum(m.price) as total_amount
    from
    	dannys_diner.sales s left join dannys_diner.menu m on s.product_id = m.product_id
    	inner join dannys_diner.members mm on s.customer_id = mm.customer_id AND s.order_date < mm.join_date
    group by
    	s.customer_id;
**ANSWER**

| customer_id | total_unit | total_amount |
| ----------- | ---------- | ------------ |
| B           | 3          | 40           |
| A           | 2          | 25           |


---

**#9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

    select
    	s.customer_id,
    	sum(case when m.product_name = 'sushi' then m.price*20 else m.price*10 end) as points
    from
    	dannys_diner.sales s left join dannys_diner.menu m on s.product_id = m.product_id
    group by
    	s.customer_id;
**ANSWER**

| customer_id | points |
| ----------- | ------ |
| B           | 940    |
| C           | 360    |
| A           | 860    |

---

**#10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

    WITH program_last_day_cte AS
      (SELECT join_date,
              join_date + INTERVAL '6 days' AS program_last_date,
              customer_id
       FROM dannys_diner.members)
    SELECT s.customer_id,
           SUM(CASE
                   WHEN order_date BETWEEN join_date AND program_last_date THEN price*10*2
                   WHEN order_date NOT BETWEEN join_date AND program_last_date
                        AND product_name = 'sushi' THEN price*10*2
                   WHEN order_date NOT BETWEEN join_date AND program_last_date
                        AND product_name != 'sushi' THEN price*10
               END) AS customer_points
    FROM dannys_diner.menu AS m
    INNER JOIN dannys_diner.sales AS s ON m.product_id = s.product_id
    INNER JOIN program_last_day_cte AS mem ON mem.customer_id = s.customer_id
    AND order_date <='2021-01-31'
    AND order_date >=join_date
    GROUP BY s.customer_id
    ORDER BY s.customer_id;

| customer_id | customer_points |
| ----------- | --------------- |
| A           | 1020            |
| B           | 320             |

---
