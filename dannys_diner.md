[Go back to README](README.md)

<div align="center"> 
<h1> SQL Solutions Danny's Diner Dataset </h1>
</div>

**<div align='center'> Problem Statement </div>**


Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:

- sales

- menu

- members

You can inspect the entity relationship diagram and example data below.

<div align="center"> 
<img width="1553" alt="Screenshot 2023-11-11 at 6 59 40 PM" src="https://github.com/afnfyz/sql_challenges/assets/124072294/763f9bfc-a37a-40b9-a96c-dcb814ffbd0f">
</div>

**<div align='center'> Case Study Questions </div>**


<details>
  <summary><strong>What is the total amount each customer spent at the restaurant?</strong></summary>

  ```SQL
  SELECT s.customer_id, sum(price)
FROM SALES s
JOIN MENU me
ON me.product_id = s.product_id
GROUP BY s.CUSTOMER_ID;
  ```
  Customer spending details...
</details>

<br>

<details>
  <summary><strong>How many days has each customer visited the restaurant? What was the first item from the menu purchased by each customer?</strong></summary>
Days Visited:
 
 ```SQL
 SELECT s.customer_id, count(DISTINCT s.order_date)
FROM SALES s
GROUP BY s.CUSTOMER_ID;
```
First Orders for Each Customer:

```SQL
SELECT s.customer_id,
       first_order.first_order_date,
       me.product_name
FROM (
    SELECT customer_id,
           MIN(order_date) AS first_order_date
    FROM SALES
    GROUP BY customer_id
) AS first_order
JOIN SALES s ON s.customer_id = first_order.customer_id
JOIN MENU me ON me.product_id = s.product_id
WHERE s.order_date = first_order.first_order_date;
```

  Customer visit and first purchase details...
</details>

<br>

<details>
  <summary><strong>What is the most purchased item on the menu and how many times was it purchased by all customers?</strong></summary>

```SQL
SELECT s.customer_id, me.product_name, count(*) as "number of times purchased"
FROM SALES s
JOIN MENU me
ON me.product_id = s.product_id
GROUP BY s.customer_id, me.product_name;
```

  Most purchased item details...
</details>

<br>

<details>
  <summary><strong>Which item was the most popular for each customer?</strong></summary>

 ``` SQL 
SELECT s.customer_id,
       me.product_name,
       COUNT(*) AS number_of_times_purchased
FROM SALES s
JOIN MENU me ON me.product_id = s.product_id
GROUP BY s.customer_id, me.product_name
HAVING COUNT(*) = (
    SELECT MAX(purchase_count)
    FROM (
        SELECT customer_id, product_id, COUNT(*) AS purchase_count
        FROM SALES
        GROUP BY customer_id, product_id
    ) AS max_counts
    WHERE max_counts.customer_id = s.customer_id
)
ORDER BY s.customer_id;
```
  Most popular item for each customer details...
</details>

<br>

<details>
  <summary><strong>Which item was purchased first by the customer after they became a member?</strong></summary>

  <!-- Your detailed explanation goes here -->
  First purchase after becoming a member details...
</details>

<br>

<details>
  <summary><strong>Which item was purchased just before the customer became a member?</strong></summary>

  <!-- Your detailed explanation goes here -->
  Purchase just before becoming a member details...
</details>

<br>

<details>
  <summary><strong>What is the total items and amount spent for each member before they became a member?</strong></summary>

  <!-- Your detailed explanation goes here -->
  Member spending details before joining...
</details>

<br>

<details>
  <summary><strong>If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?</strong></summary>

```SQL
SELECT s.customer_id, 
       sum(me.price * CASE WHEN me.product_name = 'sushi' THEN 20 ELSE 10 END) AS "Points"
FROM SALES s
JOIN MENU me
ON me.product_id = s.product_id
GROUP BY s.customer_id;
```
  Points calculation details...
</details>

<br>

<details>
  <summary><strong>In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?</strong></summary>

  <!-- Your detailed explanation goes here -->
  Points calculation for the first week details...
</details>
