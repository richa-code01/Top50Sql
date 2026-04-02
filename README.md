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
