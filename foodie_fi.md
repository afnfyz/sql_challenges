1. How many customers has Foodie-Fi ever had?
SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions;

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
SELECT
    DATE_PART('month', s.start_date) AS month,
    COUNT(DISTINCT s.customer_id) AS customer_count
FROM
    foodie_fi.subscriptions s
JOIN
    foodie_fi.plans p ON s.plan_id = p.plan_id
WHERE
    p.plan_name = 'trial'
GROUP BY
    month
ORDER BY
    customer_count DESC;

3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name

SELECT
    p.plan_name,
    COUNT(*) AS count_after_2020
FROM
    foodie_fi.subscriptions s
JOIN
    foodie_fi.plans p ON s.plan_id = p.plan_id
WHERE
    DATE_PART('year', s.start_date) > 2020
GROUP BY
    p.plan_name
ORDER BY
    count_after_2020 DESC;

4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

