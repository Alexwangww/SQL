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

**2. How many days has each customer visited the restaurant?**

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


---

**#4. What is the most purchased item on the menu and how many times was it purchased by all customers?
select**

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


---

**#6. Which item was purchased first by the customer after they became a member?**

    select
    	s.customer_id,
    	m.product_name
    from
    	dannys_diner.sales s left join dannys_diner.menu m on s.product_id = m.product_id
    	inner join dannys_diner.members mm on s.customer_id = mm.customer_id AND s.order_date >= mm.join_date
    group by
    	s.customer_id, m.product_name
    order by 
    	s.customer_id;
**ANSWER**

| customer_id | product_name |
| ----------- | ------------ |
| A           | ramen        |
| A           | curry        |
| B           | sushi        |
| B           | ramen        |

---

**#7. Which item was purchased just before the customer became a member?**


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



