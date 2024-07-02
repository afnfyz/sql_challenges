
**<div align="center"> SQL Solutions for Preppin' Data - 2023 Week 1 </div>**

<div align="center"> 
<img width="830" alt="image" src="https://github.com/afnfyz/sql_challenges/assets/124072294/305157ec-efc4-44a8-908d-3e95224a2825">
</div>

<br>

**Challenge Link:** [Summer of SQL](https://github.com/wjsutton/the_summer_of_sql)

**Requirements:**
- Split the Transaction Code to extract the letters at the start of the transaction code. These identify the bank who processes the transaction.
- Rename the new field with the Bank code 'Bank'. 
- Rename the values in the Online or In-person field: 'Online' for the 1 values and 'In-Person' for the 2 values.
- Change the date to be the day of the week.

```sql
SELECT
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g') AS Bank,
    TO_CHAR(TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'), 'Day') AS transaction_date,
    CASE
        WHEN online_or_in_person = 1 THEN 'Online'
        WHEN online_or_in_person = 2 THEN 'In-Person'
        ELSE 'Unknown'
    END AS online_or_in_person
FROM pd2023_wk01
LIMIT 100;
```
---

<details>
<summary><strong>Task 1: List of Transactions with Bank Codes and Transaction Types</strong></summary>

```sql
SELECT
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g') AS Bank,
    TO_CHAR(TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'), 'Day') AS transaction_day_of_week,
    CASE
        WHEN online_or_in_person = 1 THEN 'Online'
        WHEN online_or_in_person = 2 THEN 'In-Person'
        ELSE 'Unknown'
    END AS transaction_type
FROM pd2023_wk01
LIMIT 100;
```
</details>

---

<details>
<summary><strong>Task 2: Total Values of Transactions by Each Bank</strong></summary>

```sql
SELECT
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g') AS Bank,
    SUM(value) AS total_value
FROM pd2023_wk01
GROUP BY Bank;
```
</details>

---

<details>
<summary><strong>Task 3: Total Values by Bank, Day of the Week, and Transaction Type</strong></summary>

```sql
SELECT
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g') AS Bank,
    TO_CHAR(TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'), 'Day') AS transaction_day_of_week,
    CASE
        WHEN online_or_in_person = 1 THEN 'Online'
        WHEN online_or_in_person = 2 THEN 'In-Person'
        ELSE 'Unknown'
    END AS transaction_type,
    SUM(value) AS total_value
FROM pd2023_wk01
GROUP BY
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g'),
    TO_CHAR(TO_TIMESTAMP(transaction_date, 'DD/MM/YYYY HH24:MI:SS'), 'Day'),
    online_or_in_person;
```
</details>

---

<details>
<summary><strong>Task 4: Total Values by Bank and Customer Code</strong></summary>

```sql
SELECT
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g') AS Bank,
    customer_code,
    SUM(value) AS total_value
FROM pd2023_wk01
GROUP BY
    regexp_replace(transaction_code, '[^A-Za-z]', '', 'g'),
    customer_code;
```
</details>