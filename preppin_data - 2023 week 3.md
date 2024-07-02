**<div align="center"> SQL Solutions for Preppin' Data - 2023 Week 3 </div>**

<div align="center"> 
<img width="830" alt="image" src="https://github.com/afnfyz/sql_challenges/assets/124072294/305157ec-efc4-44a8-908d-3e95224a2825">
</div>

<br>

**Challenge Link:** [Summer of SQL](https://github.com/wjsutton/the_summer_of_sql)

**Requirements:**

For the transactions file:
- Filter the transactions to just look at DSB.
  - These will be transactions that contain DSB in the Transaction Code field.
- Rename the values in the Online or In-person field: Online for the 1 values and In-Person for the 2 values.
- Change the date to be the quarter.
- Sum the transaction values for each quarter and for each Type of Transaction (Online or In-Person).

For the targets file:
- Pivot the quarterly targets so we have a row for each Type of Transaction and each Quarter.
- Rename the fields.
- Remove the 'Q' from the quarter field and make the data type numeric.

Join the two datasets together:
- You may need more than one join clause!

---

<details>
<summary><strong>Task 1: Filter Transactions and Extract Quarters</strong></summary>

```sql
SELECT 
    transaction_code,
    CASE
        WHEN online_or_in_person = 1 THEN 'Online'
        WHEN online_or_in_person = 2 THEN 'In-Person'
    END AS online_or_in_person,
    EXTRACT(QUARTER FROM TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS')) AS quarter
FROM pd2023_wk01
WHERE transaction_code ILIKE '%DSB%';
```
</details>

---

<details>
<summary><strong>Task 2: Sum Transaction Values by Quarter and Type</strong></summary>

```sql
WITH transaction_sums AS (
    SELECT 
        CASE
            WHEN online_or_in_person = 1 THEN 'Online'
            WHEN online_or_in_person = 2 THEN 'In-Person'
        END AS online_or_in_person,
        EXTRACT(QUARTER FROM TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS')) AS quarter,
        SUM(value) AS total_value
    FROM pd2023_wk01
    WHERE transaction_code ILIKE '%DSB%'
    GROUP BY 
        CASE
            WHEN online_or_in_person = 1 THEN 'Online'
            WHEN online_or_in_person = 2 THEN 'In-Person'
        END,
        EXTRACT(QUARTER FROM TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'))
)
SELECT * FROM transaction_sums;
```
</details>

---

<details>
<summary><strong>Task 3: Transform Quarterly Targets</strong></summary>

```sql
WITH transformed_targets AS (
    SELECT 
        online_or_in_person, 
        1 AS quarter, 
        q1 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        2 AS quarter, 
        q2 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        3 AS quarter, 
        q3 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        4 AS quarter, 
        q4 AS target
    FROM 
        pd2023_wk03_targets
)
SELECT * FROM transformed_targets;
```
</details>

---

<details>
<summary><strong>Task 4: Join Datasets and Calculate Variance to Target</strong></summary>

```sql
WITH transformed_targets AS (
    SELECT 
        online_or_in_person, 
        1 AS quarter, 
        q1 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        2 AS quarter, 
        q2 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        3 AS quarter, 
        q3 AS target
    FROM 
        pd2023_wk03_targets
    UNION ALL
    SELECT 
        online_or_in_person, 
        4 AS quarter, 
        q4 AS target
    FROM 
        pd2023_wk03_targets
),
transaction_sums AS (
    SELECT 
        CASE
            WHEN online_or_in_person = 1 THEN 'Online'
            WHEN online_or_in_person = 2 THEN 'In-Person'
        END AS online_or_in_person,
        EXTRACT(QUARTER FROM TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS')) AS quarter,
        SUM(value) AS total_value
    FROM pd2023_wk01
    WHERE transaction_code ILIKE '%DSB%'
    GROUP BY 
        CASE
            WHEN online_or_in_person = 1 THEN 'Online'
            WHEN online_or_in_person = 2 THEN 'In-Person'
        END,
        EXTRACT(QUARTER FROM TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'))
)
SELECT 
    t.online_or_in_person, 
    t.quarter, 
    t.total_value, 
    tr.target,
    t.total_value - tr.target AS variance_to_target
FROM 
    transaction_sums t
JOIN 
    transformed_targets tr
ON 
    t.online_or_in_person = tr.online_or_in_person
AND 
    t.quarter = tr.quarter;
```
</details>

```sql
