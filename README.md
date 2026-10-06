# SQL Interview Questions & Answers

## Project Overview

This repository contains SQL interview questions, answers, and practical query examples for **Data Analyst and SQL-related interviews**.

The questions cover SQL fundamentals, filtering, aggregation, subqueries, CTEs, window functions, ranking, joins, date analysis, customer analysis, product analysis, and business-oriented SQL problems.

---

# 1. SQL Fundamentals

### Q1. What is SQL?

**Answer:**
SQL (Structured Query Language) is used to communicate with relational databases. It is used to retrieve, filter, analyze, insert, update, and delete data.

---

### Q2. What is a database?

**Answer:**
A database is an organized collection of data that can be stored, accessed, managed, and analyzed efficiently.

---

### Q3. What is a table?

**Answer:**
A table stores data in rows and columns. Each row represents a record and each column represents an attribute.

---

### Q4. What is the difference between a row and a column?

**Answer:**
A row represents one record, while a column represents a specific attribute or field of the data.

---

### Q5. What is a primary key?

**Answer:**
A primary key uniquely identifies each record in a table. It cannot contain duplicate or NULL values.

---

### Q6. What is a foreign key?

**Answer:**
A foreign key is a column that creates a relationship between two tables by referencing a primary key or unique key in another table.

---

# 2. SELECT and Filtering

### Q7. What is SELECT used for?

**Answer:**
`SELECT` is used to retrieve data from a database table.

```sql
SELECT customer_name, amount
FROM orders;
```

---

### Q8. What is WHERE?

**Answer:**
`WHERE` filters individual rows based on a condition.

```sql
SELECT *
FROM orders
WHERE amount > 500;
```

---

### Q9. What is the difference between WHERE and HAVING?

**Answer:**

- `WHERE` filters rows before grouping.
- `HAVING` filters groups after aggregation.

```sql
SELECT city, SUM(amount) AS total_sales
FROM orders
GROUP BY city
HAVING SUM(amount) > 5000;
```

---

### Q10. What are AND and OR?

**Answer:**
They are logical operators used to combine conditions.

```sql
SELECT *
FROM orders
WHERE city = 'Berlin'
AND amount > 500;
```

---

### Q11. What is IN?

**Answer:**
`IN` checks whether a value exists in a list of values.

```sql
SELECT *
FROM orders
WHERE city IN ('Berlin', 'Munich');
```

---

### Q12. What is BETWEEN?

**Answer:**
`BETWEEN` filters values within a specified range.

```sql
SELECT *
FROM orders
WHERE amount BETWEEN 200 AND 500;
```

---

### Q13. What is LIKE?

**Answer:**
`LIKE` is used for pattern matching.

```sql
SELECT *
FROM orders
WHERE customer_name LIKE 'A%';
```

`%` represents zero or more characters.

---

# 3. Sorting and Aggregation

### Q14. What is ORDER BY?

**Answer:**
`ORDER BY` sorts query results.

```sql
SELECT *
FROM orders
ORDER BY amount DESC;
```

---

### Q15. What is LIMIT?

**Answer:**
`LIMIT` restricts the number of rows returned.

```sql
SELECT *
FROM orders
ORDER BY amount DESC
LIMIT 5;
```

---

### Q16. What are aggregate functions?

**Answer:**
Aggregate functions perform calculations on multiple rows.

Common functions:

- `COUNT()`
- `SUM()`
- `AVG()`
- `MIN()`
- `MAX()`

---

### Q17. How do you calculate total sales?

```sql
SELECT SUM(amount) AS total_sales
FROM orders;
```

**Answer:**
`SUM()` adds all values in the selected column.

---

### Q18. How do you calculate average order value?

```sql
SELECT AVG(amount) AS average_order_value
FROM orders;
```

---

### Q19. How do you count orders?

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

---

### Q20. What is GROUP BY?

**Answer:**
`GROUP BY` groups rows with the same values so aggregate calculations can be performed.

```sql
SELECT city, SUM(amount) AS total_sales
FROM orders
GROUP BY city;
```

---

# 4. CASE and Conditional Logic

### Q21. What is CASE WHEN?

**Answer:**
`CASE WHEN` applies conditional logic in SQL.

```sql
SELECT order_id,
       amount,
       CASE
           WHEN amount < 200 THEN 'Low'
           WHEN amount <= 500 THEN 'Medium'
           ELSE 'High'
       END AS order_category
FROM orders;
```

It is useful for classification and business rules.

---

# 5. Subqueries

### Q22. What is a subquery?

**Answer:**
A subquery is a query inside another SQL query.

Example:

```sql
SELECT *
FROM orders
WHERE amount > (
    SELECT AVG(amount)
    FROM orders
);
```

This finds orders above the average order amount.

---

### Q23. How do you find the second-highest order amount?

```sql
SELECT MAX(amount) AS second_highest
FROM orders
WHERE amount < (
    SELECT MAX(amount)
    FROM orders
);
```

---

### Q24. How do you find customers spending above average?

**Answer:**

First calculate spending for each customer, then compare it with average customer spending.

```sql
SELECT customer_name,
       SUM(amount) AS total_spending
FROM orders
GROUP BY customer_name
HAVING SUM(amount) > (
    SELECT AVG(customer_total)
    FROM (
        SELECT customer_name,
               SUM(amount) AS customer_total
        FROM orders
        GROUP BY customer_name
    ) AS customer_data
);
```

---

# 6. CTEs

### Q25. What is a CTE?

**Answer:**
CTE stands for Common Table Expression. It creates a temporary named result that can be used by the main query.

```sql
WITH sales_data AS (
    SELECT city,
           SUM(amount) AS total_sales
    FROM orders
    GROUP BY city
)
SELECT *
FROM sales_data;
```

CTEs make complex queries easier to read and maintain.

---

### Q26. Can we use multiple CTEs?

**Answer:**
Yes. Multiple CTEs can be used to break a complex problem into smaller steps.

```sql
WITH sales_data AS (
    SELECT city,
           SUM(amount) AS total_sales
    FROM orders
    GROUP BY city
),
ranked_data AS (
    SELECT city,
           total_sales,
           RANK() OVER (
               ORDER BY total_sales DESC
           ) AS sales_rank
    FROM sales_data
)
SELECT *
FROM ranked_data;
```

---

# 7. Window Functions

### Q27. What is a window function?

**Answer:**
A window function performs calculations across related rows without reducing the number of rows returned.

Examples:

- `RANK()`
- `ROW_NUMBER()`
- `LAG()`
- `LEAD()`
- `SUM() OVER()`
- `AVG() OVER()`

---

### Q28. What is RANK()?

**Answer:**
`RANK()` assigns rankings to rows. Equal values receive the same rank.

```sql
SELECT customer_name,
       SUM(amount) AS total_spending,
       RANK() OVER (
           ORDER BY SUM(amount) DESC
       ) AS customer_rank
FROM orders
GROUP BY customer_name;
```

---

### Q29. What is ROW_NUMBER()?

**Answer:**
`ROW_NUMBER()` assigns a unique sequential number to each row.

```sql
SELECT customer_name,
       amount,
       ROW_NUMBER() OVER (
           ORDER BY amount DESC
       ) AS row_number
FROM orders;
```

---

### Q30. Difference between RANK() and ROW_NUMBER()?

**Answer:**

`RANK()` gives the same rank to tied values.

`ROW_NUMBER()` always gives every row a unique number.

---

### Q31. What is PARTITION BY?

**Answer:**
`PARTITION BY` divides data into groups before applying a window function.

```sql
ROW_NUMBER() OVER (
    PARTITION BY city
    ORDER BY amount DESC
)
```

This can be used to find the top orders within each city.

---

### Q32. What is LAG()?

**Answer:**
`LAG()` retrieves a value from a previous row.

```sql
SELECT customer_name,
       order_date,
       amount,
       LAG(amount) OVER (
           PARTITION BY customer_name
           ORDER BY order_date
       ) AS previous_order
FROM orders;
```

---

### Q33. What is a running total?

**Answer:**
A running total is a cumulative sum calculated over ordered rows.

```sql
SELECT order_date,
       amount,
       SUM(amount) OVER (
           ORDER BY order_date
       ) AS running_total
FROM orders;
```

---

# 8. JOINs

### Q34. What is a JOIN?

**Answer:**
A JOIN combines data from two or more tables using a related column.

Common JOIN types:

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN

---

### Q35. What is INNER JOIN?

**Answer:**
`INNER JOIN` returns only records that have matching values in both tables.

```sql
SELECT *
FROM customers c
INNER JOIN orders o
ON c.customer_id = o.customer_id;
```

---

### Q36. What is LEFT JOIN?

**Answer:**
`LEFT JOIN` returns all records from the left table and matching records from the right table.

---

### Q37. Difference between INNER JOIN and LEFT JOIN?

**Answer:**

- `INNER JOIN` → only matching records.
- `LEFT JOIN` → all left-table records plus matching right-table records.

---

# 9. NULL and Duplicates

### Q38. What is NULL?

**Answer:**
`NULL` represents a missing or unknown value. It is different from zero or an empty string.

---

### Q39. How do you find NULL values?

```sql
SELECT *
FROM orders
WHERE amount IS NULL;
```

---

### Q40. How do you find duplicate values?

```sql
SELECT customer_name,
       COUNT(*) AS total
FROM orders
GROUP BY customer_name
HAVING COUNT(*) > 1;
```

---

# 10. Data Analysis Questions

### Q41. How do you find the top 5 customers by spending?

```sql
SELECT customer_name,
       SUM(amount) AS total_spending
FROM orders
GROUP BY customer_name
ORDER BY total_spending DESC
LIMIT 5;
```

---

### Q42. How do you find total sales by city?

```sql
SELECT city,
       SUM(amount) AS total_sales
FROM orders
GROUP BY city
ORDER BY total_sales DESC;
```

---

### Q43. How do you find the highest-value order in each city?

**Answer:**
Use a window function with `PARTITION BY`.

```sql
WITH ranked_orders AS (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY city
               ORDER BY amount DESC
           ) AS rn
    FROM orders
)
SELECT *
FROM ranked_orders
WHERE rn = 1;
```

---

### Q44. How do you find the top 3 orders in each city?

```sql
WITH ranked_orders AS (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY city
               ORDER BY amount DESC
           ) AS rn
    FROM orders
)
SELECT *
FROM ranked_orders
WHERE rn <= 3;
```

---

### Q45. How do you find the most popular product?

**Answer:**
Group products by quantity and sort from highest to lowest.

```sql
SELECT product,
       SUM(quantity) AS total_quantity
FROM orders
GROUP BY product
ORDER BY total_quantity DESC
LIMIT 1;
```

---

# 11. Date Analysis

### Q46. How do you calculate monthly sales?

```sql
SELECT DATE_FORMAT(order_date, '%Y-%m') AS sales_month,
       SUM(amount) AS total_sales
FROM orders
GROUP BY DATE_FORMAT(order_date, '%Y-%m')
ORDER BY sales_month;
```

---

### Q47. How do you calculate month-over-month sales?

**Answer:**
First calculate monthly sales and then use `LAG()` to compare the current month with the previous month.

```sql
WITH monthly_sales AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS sales_month,
           SUM(amount) AS total_sales
    FROM orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
)
SELECT sales_month,
       total_sales,
       LAG(total_sales) OVER (
           ORDER BY sales_month
       ) AS previous_month_sales
FROM monthly_sales;
```

---

# 12. Business Analysis

### Q48. How would you find the highest-selling category?

```sql
SELECT category,
       SUM(amount) AS total_sales
FROM orders
GROUP BY category
ORDER BY total_sales DESC
LIMIT 1;
```

---

### Q49. How would you calculate each category's percentage of total sales?

```sql
SELECT category,
       SUM(amount) AS total_sales,
       ROUND(
           SUM(amount) * 100.0 /
           SUM(SUM(amount)) OVER (),
           2
       ) AS sales_percentage
FROM orders
GROUP BY category;
```

---

### Q50. How would you explain your SQL project in an interview?

**Answer:**

> "I created a SQL data analysis project using a 100-row e-commerce dataset. I solved 30 practical business problems covering filtering, aggregation, GROUP BY, HAVING, subqueries, CTEs, window functions, ranking, customer analysis, product analysis, sales trends, running totals, and month-over-month analysis. The main goal was to strengthen my SQL skills and learn how to convert business questions into SQL queries and useful insights."

---

# Key SQL Concepts Learned

- SELECT
- WHERE
- ORDER BY
- LIMIT
- AND / OR
- IN
- BETWEEN
- LIKE
- COUNT()
- SUM()
- AVG()
- MIN()
- MAX()
- GROUP BY
- HAVING
- CASE WHEN
- Subqueries
- Nested Subqueries
- CTEs
- Multiple CTEs
- Window Functions
- RANK()
- ROW_NUMBER()
- LAG()
- PARTITION BY
- Running Totals
- Percentage Analysis
- Date Analysis
- Month-over-Month Analysis
- JOINs
- NULL Handling
- Duplicate Detection
- Customer Analysis
- Product Analysis
- Sales Analysis
- Business-Oriented SQL

# Tools Used

- MySQL
- MySQL Workbench
- SQL
- GitHub

# Project Goal

The goal of this repository is to build strong SQL interview knowledge and practical problem-solving skills for **Data Analyst and Data Science roles**.
