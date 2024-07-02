**<div align="center"> SQL Solutions for Preppin' Data - 2023 Week 2 </div>**

<div align="center"> 
<img width="830" alt="image" src="https://github.com/afnfyz/sql_challenges/assets/124072294/305157ec-efc4-44a8-908d-3e95224a2825">
</div>

<br>

**Challenge Link:** [Summer of SQL](https://github.com/wjsutton/the_summer_of_sql)

**Requirements:**
- In the Transactions table, there is a Sort Code field which contains dashes. We need to remove these so just have a 6 digit string.
- Use the SWIFT Bank Code lookup table to bring in additional information about the SWIFT code and Check Digits of the receiving bank account.
- Add a field for the Country Code. (Hint: all these transactions take place in the UK so the Country Code should be GB.)
- Create the IBAN as above. (Hint: watch out for trying to combine string fields with numeric fields - check data types.)
- Remove unnecessary fields.
- Output the data.

---

<details>
<summary><strong>Solution:</strong></summary>

```sql
SELECT
    transaction_id,
    'GB' || check_digits || swift_code || regexp_replace(sort_code, '-', '', 'g') || account_number AS IBAN
FROM pd2023_wk02_transactions AS transactions
JOIN pd2023_wk02_swift_codes AS swift ON transactions.bank = swift.bank
LIMIT 100;
```
</details>
