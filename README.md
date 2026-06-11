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

# Window Functions in SQL

A **window function** performs calculations across a set of related rows while **preserving all rows** in the result.

## Aggregate vs Window Function

### Aggregate Function

```sql
SELECT SUM(weight)
FROM Queue;
```

Returns a single row.

### Window Function

```sql
SELECT person_name,
       SUM(weight) OVER (ORDER BY turn) AS running_total
FROM Queue;
```

Returns every row along with a running total.

---

## Window Function Syntax

```sql
function(...) OVER (...)
```

Example:

```sql
SUM(weight) OVER (ORDER BY turn)
```

- `SUM(weight)` → calculation to perform
- `OVER(...)` → defines the window
- `ORDER BY turn` → order in which the calculation is applied

---

# LeetCode 1204: Last Person to Fit in the Bus

### Idea

Compute the **cumulative weight** of passengers in boarding order and find the last person whose cumulative weight does not exceed **1000 kg**.

### Solution

```sql
SELECT person_name
FROM (
    SELECT person_name,
           SUM(weight) OVER (ORDER BY turn) AS total_weight
    FROM Queue
) t
WHERE total_weight <= 1000
ORDER BY total_weight DESC
LIMIT 1;
```

### Example

Input:

| person_name | weight | turn |
|------------|--------|------|
| Alice | 250 | 1 |
| Bob | 175 | 2 |
| Alex | 400 | 3 |
| John | 500 | 4 |

After:

```sql
SUM(weight) OVER (ORDER BY turn)
```

| person_name | total_weight |
|------------|-------------|
| Alice | 250 |
| Bob | 425 |
| Alex | 825 |
| John | 1325 |

Filter:

```sql
WHERE total_weight <= 1000
```

| person_name | total_weight |
|------------|-------------|
| Alice | 250 |
| Bob | 425 |
| Alex | 825 |

The last valid passenger is:

```text
Alex
```

---

## Common Window Functions

```sql
ROW_NUMBER() OVER (ORDER BY salary DESC)
```

```sql
LAG(salary) OVER (ORDER BY year)
```

```sql
LEAD(salary) OVER (ORDER BY year)
```

```sql
AVG(score) OVER (ORDER BY test_id)
```

### Quick Rule

Use **window functions** when you need:

- Running totals
- Running averages
- Rankings
- Previous/next row values

without losing individual rows.


# SQL JOIN Time Complexity

Assume:

- Table A has `N` rows
- Table B has `M` rows

## Without Index (Nested Loop Join)

For each row in A, scan all rows in B.

```text
Time Complexity: O(N × M)
```

---

## With Index on Join Column

For each row in A, perform an indexed lookup in B.

```text
Time Complexity: O(N log M)
```

---

## Hash Join (Common in Modern Databases)

Build a hash table on one table and scan the other.

```text
Time Complexity: O(N + M)
```

---

## Interview / LeetCode Assumption

For queries like:

```sql
SELECT *
FROM A
JOIN B
ON A.id = B.id;
```

assume:

```text
JOIN = O(N + M)
```

unless the problem explicitly indicates the absence of indexes or requires nested scans.

### Finding Maximum Value

```sql
SELECT *
FROM table_name
ORDER BY column_name DESC
LIMIT 1;
```

- `ORDER BY ... DESC` sorts values from largest to smallest.
- `LIMIT 1` returns the row with the maximum value.
- Useful when you need the entire row, not just the maximum value.

Similarly, use `ORDER BY ... ASC LIMIT 1` to find the row with the minimum value.

### Common Table Expression (CTE) - `WITH`

```sql
WITH ids AS (
    SELECT DISTINCT requester_id AS id
    FROM RequestAccepted

    UNION

    SELECT DISTINCT accepter_id AS id
    FROM RequestAccepted
)
```

- `WITH` creates a temporary result set (CTE) that can be referenced like a table in the main query.
- Useful when a complex subquery needs to be reused or when creating an intermediate table improves readability.
- The CTE exists only for the duration of the query.

Think of it as:

```text
WITH = Create a temporary table to work with
```

### Finding Unique (Non-Repeating) Values

```sql

SELECT column_name

FROM table_name

GROUP BY column_name

HAVING COUNT(*) = 1;

```

- `GROUP BY` groups identical values together.

- `HAVING COUNT(*) = 1` keeps only values that appear exactly once.

---
### Top K Distinct Values per Group

```sql
DENSE_RANK() OVER (
    PARTITION BY group_column
    ORDER BY value DESC
)
```

- `PARTITION BY` creates groups.
- `ORDER BY ... DESC` ranks highest values first.
- `DENSE_RANK()` assigns the same rank to equal values.
- Filter using:

```sql
WHERE rank <= K
```

Useful for:
- Top 3 salaries per department
- Top K scores per category
- Highest K values per group
```

### OFFSET in SQL

```sql
LIMIT 1 OFFSET k
```

- Skips the first `k` rows after sorting/filtering.
- Returns the next row(s) specified by `LIMIT`.

Example:

```sql
SELECT DISTINCT salary
FROM Employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

Returns the **second highest distinct salary**.

---

### Returning NULL When No Row Exists

```sql
SELECT (
    subquery
) AS result;
```

- A scalar subquery returns its value if a row exists.
- If the subquery returns no rows, SQL automatically returns `NULL`.
- Useful for problems where the expected output should be `NULL` instead of an empty result.

Example:

```sql
SELECT (
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;
```

Returns `NULL` if a second highest salary does not exist.

### LIKE in SQL

`LIKE` is used for pattern matching in strings.

#### Wildcards

```sql

%  -- Matches zero or more characters

_  -- Matches exactly one character

```

---
### Important Limitation

`LIKE` supports only:

```sql

%

_

```

It does **not** support character ranges such as:

```text

[a-z]

[A-Z]

[0-9]

```

For complex pattern matching, use `REGEXP`.

Example:

```sql

WHERE mail REGEXP '^[A-Za-z][A-Za-z0-9]*@leetcode\\.com$'
