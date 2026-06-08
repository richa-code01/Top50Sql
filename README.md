# 🏆 Top 50 SQL 

<p align="center">
  <img width="100%" alt="SQL Reference Image" src="https://github.com/user-attachments/assets/0c0d429a-0210-4454-adc2-5caae55a8302" />
</p>

---

## 📌 General Syntax & Best Practices

* **Case Insensitivity:** SQL keywords are generally case-insensitive, but it is best practice to capitalize them for readability (e.g., `SELECT`, `WHERE`).
* **Column Aliasing:** Use the `AS` keyword to rename columns in your output.
    ```sql
    SELECT author_id AS id FROM table_name;
    ```
* **Table Aliases:** Use short aliases (like `e` for `employees`, `eu` for `employee_universe`) to keep queries neat and readable.

## 🔍 Filtering & Sorting

* **Handling NULLs:** **Never** use `=` to compare with `NULL`. Always use `IS NULL` or `IS NOT NULL`.
    ```sql
    WHERE column_name IS NULL
    ```
* **Sorting Results:** Use the `ORDER BY` clause. By default, it sorts in ascending (`ASC`) order.
    ```sql
    ORDER BY column_name DESC; -- Use DESC for descending order
    ```
* **WHERE  -> filters rows. HAVING -> filters groups (after GROUP BY)**

**Aggregate function in WHERE? → probably needs HAVING.**

## 🔗 Joins

* **LEFT JOIN Rules:** A `LEFT JOIN` **must** include an `ON` clause. 
* **ON vs. WHERE:** The join condition should be placed in the `ON` clause. If you place a `LEFT JOIN` condition in the `WHERE` clause, it inadvertently filters out the `NULL` records, turning it into an implicit `INNER JOIN`.

## 🛠️ Built-in Functions & Techniques

### String Operations
* **Length:** Use the `LENGTH()` function to find the character count of a string (e.g., `VARCHAR`).
    ```sql
    WHERE LENGTH(content) > 15;
    ```

### Date Operations
* **Comparing Dates:** Use the `DATEDIFF()` function, or simple date arithmetic in MySQL.
    ```sql
    -- Standard approach
    DATEDIFF(date1, date2) = 1
    
    -- MySQL specific approach
    ON w1.date - w2.date = 1
    ```

### Math & Aggregation
* **Rounding Numbers:** Use `ROUND(value, k)` to round a number to `k` decimal places.
* **Handling Null Outputs:** Utilize `IFNULL(expression, alt_value)` or `NULLIF(expr1, expr2)` to manage empty or invalid data gracefully.
* **Conditional Summation:** You can sum boolean conditions directly in MySQL to count specific occurrences.
    ```sql
    -- Sums up the rows where the condition is true
    SUM(c.action = 'confirmed') 
    ```

---

## 📝 LeetCode Specific Question Notes

### 1581. Customer Who Visited but Did Not Make Any Transactions
*(Add your specific query notes, edge cases, or solutions for this problem here)*

### Uncorrelated query

```sql

SELECT

    r.contest_id,

    COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Users)

FROM Register r

GROUP BY r.contest_id;

```

Here,

```sql

(SELECT COUNT(*) FROM Users)

```

is an **uncorrelated subquery** because it does not reference any column from the outer query (`r`).

Since it is independent of the outer query, the database optimizer typically evaluates it **once**:

```text

SELECT COUNT(*) FROM Users

→ 5

```

and then reuses that value for every group.

### Selecting a Specific Row from Each Group

Suppose we want the **first order of every customer**.

```sql
SELECT
    ROUND(
        AVG(customer_pref_delivery_date = order_date) * 100,
        2
    ) AS immediate_percentage
FROM Delivery d
WHERE (customer_id, order_date) IN (
    SELECT
        customer_id,
        MIN(order_date)
    FROM Delivery
    GROUP BY customer_id
);
```

This returns the row(s) containing the earliest order date for each customer.
**Having** is used when we want to filter whole group itself.
---
### GROUP BY Rule

After a `GROUP BY`, every column in the `SELECT` clause must be either:

1. Present in the `GROUP BY` clause, **or**
2. Wrapped in an aggregate function such as:
   - `SUM()`
   - `COUNT()`
   - `MIN()`
   - `MAX()`
   - `AVG()`

Otherwise, the query is **invalid** (in standard SQL and MySQL with `ONLY_FULL_GROUP_BY` enabled) or may produce **nondeterministic results** (in some MySQL configurations i.e choose any arbitrary value fromt the group for that column).

#### Valid Examples

```sql
SELECT product_id, COUNT(*)
FROM Sales
GROUP BY product_id;

### Adding Days to a Date in MySQL

#### Add 1 Day

```sql
SELECT DATE_ADD(order_date, INTERVAL 1 DAY)
FROM Delivery;
```
#### Subtract 1 Day

```sql
SELECT DATE_SUB(order_date, INTERVAL 1 DAY)
FROM Delivery;
```

---

### Quick Reference

| Task | MySQL Syntax |
|--------|--------|
| Earliest date | `MIN(date_col)` |
| Latest date | `MAX(date_col)` |
| Add 1 day | `DATE_ADD(date_col, INTERVAL 1 DAY)` |
| Subtract 1 day | `DATE_SUB(date_col, INTERVAL 1 DAY)` |
| Extract month | `MONTH(date_col)` |
| Extract year | `YEAR(date_col)` |
| Extract year-month | `DATE_FORMAT(date_col, '%Y-%m')` |
| Select row with min value per group | `WHERE (group_col, value_col) IN (SELECT group_col, MIN(value_col) ... GROUP BY group_col)` |
