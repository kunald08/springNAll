# 🔥 Top 30 SQL Interview Problems (Easy → Hard)

---

## 🟢 EASY (1–10)

---

### 1. Second Highest Salary

**Approach:** Use a **subquery** to first find the MAX salary, then find the MAX salary that is strictly less than it. The `DENSE_RANK()` alternative assigns ranks without gaps — rank 2 is always the second highest regardless of ties. `DENSE_RANK` is preferred over `RANK` because `RANK` skips numbers on ties (e.g., 1,1,3 instead of 1,1,2).

```sql
SELECT MAX(salary) AS SecondHighestSalary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

**Alt (using DENSE_RANK):**
```sql
SELECT salary AS SecondHighestSalary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk = 2;
```

---

### 2. Find Duplicate Emails

**Approach:** **GROUP BY + HAVING**. Group all rows by `email`, then filter groups where `COUNT(*) > 1`. `HAVING` is used instead of `WHERE` because we're filtering on an aggregate result (count), not on individual rows. This is the most common pattern for finding duplicates.

```sql
SELECT email
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

---

### 3. Employees Earning More Than Their Manager

**Approach:** **Self Join** — join the `employees` table to itself. Alias `e` represents the employee and `m` represents their manager. The join condition `e.manager_id = m.id` links each employee to their manager's row. Then simply compare `e.salary > m.salary` in the WHERE clause.

```sql
SELECT e.name AS Employee
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

---

### 4. Customers Who Never Ordered

**Approach:** **LEFT JOIN + IS NULL**. Left join keeps all customers even if they have no matching orders. Customers without orders will have `NULL` in the orders columns — filter with `WHERE o.id IS NULL`. Alternative: `NOT IN` or `NOT EXISTS` subquery, but LEFT JOIN is generally more readable and performs well.

```sql
SELECT c.name AS Customer
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.id IS NULL;
```

---

### 5. Delete Duplicate Rows (Keep One)

**Approach:** **Keep the row with the smallest `id`** for each duplicate group. The subquery `SELECT MIN(id) FROM employees GROUP BY email` finds one survivor per email. Delete everything NOT in that list. In Oracle, use `ROWID` instead of `id` since it's a physical row identifier that always uniquely identifies a row.

```sql
DELETE FROM employees
WHERE id NOT IN (
    SELECT MIN(id)
    FROM employees
    GROUP BY email
);
```

**Oracle-specific:**
```sql
DELETE FROM employees
WHERE ROWID NOT IN (
    SELECT MIN(ROWID)
    FROM employees
    GROUP BY email
);
```

---

### 6. Find Nth Highest Salary (Generic)

**Approach:** **DENSE_RANK()** in a subquery. Rank all distinct salaries in descending order, then filter where rank = N. `DENSE_RANK` handles ties correctly — if two people share the highest salary, the next distinct salary is still rank 2. Using `DISTINCT` on salary ensures we rank unique salary values, not individual rows.

```sql
SELECT salary
FROM (
    SELECT DISTINCT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk = :N;
```

---

### 7. Combine Two Tables (LEFT JOIN)

**Approach:** **LEFT JOIN** ensures every person appears in the result even if they don't have an address record. If we used INNER JOIN, persons without addresses would be dropped. The key insight: the question says "even if address is missing" — that's the signal for LEFT JOIN.

> Display person info with address (even if address is missing).

```sql
SELECT p.first_name, p.last_name, a.city, a.state
FROM person p
LEFT JOIN address a ON p.person_id = a.person_id;
```

---

### 8. Swap Salary (Male ↔ Female)

**Approach:** **Single UPDATE with CASE expression**. No need for a temp variable or multiple statements. `CASE WHEN gender = 'M' THEN 'F' ELSE 'M'` flips each value in one pass. This updates all rows atomically — much cleaner than doing two separate updates which could cause conflicts.

```sql
UPDATE employees
SET gender = CASE
    WHEN gender = 'M' THEN 'F'
    ELSE 'M'
END;
```

---

### 9. Rising Temperature — Find Dates Where Temp > Previous Day

**Approach:** **Self Join on consecutive dates** — join each day's row with the previous day's row using `date + 1`. Then compare temperatures. The **LAG()** alternative is cleaner: `LAG(temperature) OVER (ORDER BY record_date)` gives you the previous day's temp directly without a join, then just filter where current > previous.

```sql
SELECT w1.id
FROM weather w1
JOIN weather w2
  ON w1.record_date = w2.record_date + 1
WHERE w1.temperature > w2.temperature;
```

**Using LAG:**
```sql
SELECT id
FROM (
    SELECT id, temperature,
           LAG(temperature) OVER (ORDER BY record_date) AS prev_temp
    FROM weather
) t
WHERE temperature > prev_temp;
```

---

### 10. Count Employees in Each Department

**Approach:** **LEFT JOIN + GROUP BY + COUNT**. Left join from `departments` to `employees` ensures departments with zero employees still appear (with count 0). Use `COUNT(e.id)` not `COUNT(*)` — `COUNT(*)` would return 1 for empty departments (counts the NULL row), while `COUNT(e.id)` correctly returns 0.

```sql
SELECT d.name AS department, COUNT(e.id) AS employee_count
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
GROUP BY d.name
ORDER BY employee_count DESC;
```

---

## 🟡 MEDIUM (11–20)

---

### 11. Department Highest Salary

**Approach:** **Correlated subquery with tuple comparison**. The subquery finds the MAX salary per department. The outer query matches rows where `(department_id, salary)` matches any `(department_id, MAX(salary))` pair. This handles ties — if two employees share the max salary, both appear. Alternative: use `DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC)` and filter rank = 1.

```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary
FROM employees e
JOIN departments d ON e.department_id = d.id
WHERE (e.department_id, e.salary) IN (
    SELECT department_id, MAX(salary)
    FROM employees
    GROUP BY department_id
);
```

---

### 12. Rank Scores (No Gaps)

**Approach:** **DENSE_RANK()** — ranks values with no gaps. If scores are 100, 100, 90, the ranks are 1, 1, 2 (not 1, 1, 3 like `RANK()` would give). `ORDER BY score DESC` ensures the highest score gets rank 1. This is a one-liner with window functions — no subqueries or self-joins needed.

```sql
SELECT score,
       DENSE_RANK() OVER (ORDER BY score DESC) AS "rank"
FROM scores;
```

---

### 13. Consecutive Numbers — Find Numbers Appearing 3+ Times in a Row

**Approach:** **Triple self-join on consecutive IDs** — join the table to itself 3 times where IDs are consecutive (`id`, `id-1`, `id-2`) and all three values match. The **LEAD()** alternative is more elegant: look ahead 1 and 2 rows, then check if all three are equal. `DISTINCT` prevents duplicates when a number appears 4+ times in a row.

```sql
SELECT DISTINCT l1.num AS ConsecutiveNums
FROM logs l1
JOIN logs l2 ON l1.id = l2.id - 1
JOIN logs l3 ON l2.id = l3.id - 1
WHERE l1.num = l2.num AND l2.num = l3.num;
```

**Using LEAD/LAG:**
```sql
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num,
           LEAD(num, 1) OVER (ORDER BY id) AS next1,
           LEAD(num, 2) OVER (ORDER BY id) AS next2
    FROM logs
) t
WHERE num = next1 AND num = next2;
```

---

### 14. Department Top 3 Salaries

**Approach:** **DENSE_RANK() with PARTITION BY**. Rank salaries within each department (`PARTITION BY department_id`), then filter `rnk <= 3`. `DENSE_RANK` ensures ties are handled — if 3 people share the 2nd highest salary, they all get rank 2 and the next salary gets rank 3. This is the go-to pattern for "top N per group" questions.

```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary
FROM (
    SELECT *, DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) e
JOIN departments d ON e.department_id = d.id
WHERE e.rnk <= 3;
```

---

### 15. Running Total / Cumulative Sum

**Approach:** **SUM() as a window function** with `ORDER BY id`. Without `PARTITION BY`, it sums across all rows. The `ORDER BY` makes it cumulative — each row's value = sum of all rows from the start up to the current row. The default window frame is `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, which is exactly what we need.

```sql
SELECT id, name, salary,
       SUM(salary) OVER (ORDER BY id) AS running_total
FROM employees;
```

---

### 16. Find Median Salary

**Approach:** **ROW_NUMBER + COUNT trick**. Assign row numbers ordered by salary, also compute the total count. The median is at position `(total+1)/2`. For even counts, average the two middle values using `FLOOR` and `CEIL` — e.g., for 10 rows, FLOOR(5.5)=5 and CEIL(5.5)=6, so average rows 5 and 6. For odd counts, both formulas point to the same row.

```sql
SELECT AVG(salary) AS median_salary
FROM (
    SELECT salary,
           ROW_NUMBER() OVER (ORDER BY salary) AS rn,
           COUNT(*) OVER () AS total
    FROM employees
) t
WHERE rn IN (FLOOR((total + 1) / 2.0), CEIL((total + 1) / 2.0));
```

---

### 17. Year-over-Year Growth

**Approach:** **Self-join on year = year + 1** to pair each year with its previous year. Calculate growth as `(current - previous) / previous * 100`. The **LAG()** version is cleaner — `LAG(revenue) OVER (ORDER BY year)` directly gives the previous year's revenue without a join. LEFT JOIN / LAG handles the first year gracefully (previous = NULL → growth = NULL).

```sql
SELECT
    curr.year,
    curr.revenue,
    prev.revenue AS prev_revenue,
    ROUND((curr.revenue - prev.revenue) * 100.0 / prev.revenue, 2) AS yoy_growth_pct
FROM yearly_sales curr
LEFT JOIN yearly_sales prev ON curr.year = prev.year + 1;
```

**Using LAG:**
```sql
SELECT year, revenue,
       LAG(revenue) OVER (ORDER BY year) AS prev_revenue,
       ROUND((revenue - LAG(revenue) OVER (ORDER BY year)) * 100.0
             / LAG(revenue) OVER (ORDER BY year), 2) AS yoy_growth_pct
FROM yearly_sales;
```

---

### 18. Pivot — Rows to Columns

**Approach:** **Conditional aggregation using CASE + SUM**. For each quarter column, use `SUM(CASE WHEN quarter = 'Q1' THEN amount ELSE 0 END)`. This transforms vertical quarter rows into horizontal columns. `GROUP BY product` collapses all rows per product into one. This is the most portable pivot technique — works in all SQL dialects without vendor-specific `PIVOT` syntax.

> Show total sales per product for each quarter.

```sql
SELECT product,
    SUM(CASE WHEN quarter = 'Q1' THEN amount ELSE 0 END) AS Q1,
    SUM(CASE WHEN quarter = 'Q2' THEN amount ELSE 0 END) AS Q2,
    SUM(CASE WHEN quarter = 'Q3' THEN amount ELSE 0 END) AS Q3,
    SUM(CASE WHEN quarter = 'Q4' THEN amount ELSE 0 END) AS Q4
FROM sales
GROUP BY product;
```

---

### 19. Self Join — Find Employees in the Same Department

**Approach:** **Self Join with inequality condition**. Join `employees` to itself on matching `department_id`. The condition `e1.id < e2.id` ensures each pair appears only once (avoids both (A,B) and (B,A)) and prevents pairing an employee with themselves. Without `<`, you'd get duplicate pairs and self-pairs.

```sql
SELECT e1.name AS employee1, e2.name AS employee2, e1.department_id
FROM employees e1
JOIN employees e2
  ON e1.department_id = e2.department_id
  AND e1.id < e2.id;
```

---

### 20. Find Gaps in Sequential IDs

**Approach:** **LAG() to compare each ID with its predecessor**. If `current_id - previous_id > 1`, there's a gap. The gap starts at `prev_id + 1` and ends at `curr_id - 1`. For example, if IDs jump from 5 to 9, the gap is 6–8. LAG is perfect here — no self-join needed. This is a classic **gap analysis** pattern.

```sql
SELECT (prev_id + 1) AS gap_start, (curr_id - 1) AS gap_end
FROM (
    SELECT id AS curr_id,
           LAG(id) OVER (ORDER BY id) AS prev_id
    FROM employees
) t
WHERE curr_id - prev_id > 1;
```

---

## 🔴 HARD (21–30)

---

### 21. Trips and Users — Cancellation Rate (LeetCode 262)

**Approach:** **Double JOIN to filter banned users + conditional aggregation**. Join trips with users twice (once for client, once for driver) to exclude banned users. Then use `SUM(CASE WHEN status LIKE 'cancelled%' ...)` / `COUNT(*)` to calculate the cancellation ratio per day. The key insight: filter banned users via JOIN conditions, not WHERE, to keep the logic clean.

```sql
SELECT
    t.request_at AS Day,
    ROUND(SUM(CASE WHEN t.status LIKE 'cancelled%' THEN 1 ELSE 0 END)
          / COUNT(*), 2) AS "Cancellation Rate"
FROM trips t
JOIN users u1 ON t.client_id = u1.users_id AND u1.banned = 'No'
JOIN users u2 ON t.driver_id = u2.users_id AND u2.banned = 'No'
WHERE t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY t.request_at;
```

---

### 22. Recursive CTE — Employee Hierarchy

**Approach:** **Recursive CTE** has two parts: (1) **Anchor** — select the root nodes (employees with no manager, level = 1), (2) **Recursive member** — join the CTE to itself to find subordinates, incrementing the level. The recursion stops when no more joins match. Oracle's `CONNECT BY PRIOR` is the proprietary equivalent — `START WITH` is the anchor, `CONNECT BY PRIOR id = manager_id` is the recursive step.

```sql
WITH RECURSIVE emp_tree AS (
    -- Anchor: top-level managers
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: subordinates
    SELECT e.id, e.name, e.manager_id, et.level + 1
    FROM employees e
    JOIN emp_tree et ON e.manager_id = et.id
)
SELECT * FROM emp_tree ORDER BY level, name;
```

**Oracle syntax (CONNECT BY):**
```sql
SELECT id, name, manager_id, LEVEL
FROM employees
START WITH manager_id IS NULL
CONNECT BY PRIOR id = manager_id
ORDER SIBLINGS BY name;
```

---

### 23. Find Users With Consecutive Login Streaks ≥ 3 Days

**Approach:** **Row number subtraction trick** (island & gap technique). For consecutive dates, `login_date - ROW_NUMBER()` produces the same value — this becomes the group identifier. E.g., dates Jan 3,4,5 with row numbers 1,2,3 → subtracting gives Jan 2, Jan 2, Jan 2 (same group). Then count each group's size and filter for streaks ≥ 3. First deduplicate logins to handle multiple logins per day.

```sql
WITH numbered AS (
    SELECT user_id, login_date,
           login_date - ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) * INTERVAL '1 day' AS grp
    FROM (SELECT DISTINCT user_id, login_date FROM logins) t
),
streaks AS (
    SELECT user_id, COUNT(*) AS streak_len
    FROM numbered
    GROUP BY user_id, grp
)
SELECT DISTINCT user_id
FROM streaks
WHERE streak_len >= 3;
```

---

### 24. Moving Average (Last 3 Rows)

**Approach:** **Window function with explicit frame** — `AVG(amount) OVER (ORDER BY sale_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)`. The `ROWS BETWEEN` clause defines a sliding window of exactly 3 rows (current + 2 before). For the first 1–2 rows, the window is smaller (only available rows are used). This is how real-time dashboards compute rolling averages.

```sql
SELECT id, sale_date, amount,
       ROUND(AVG(amount) OVER (
           ORDER BY sale_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ), 2) AS moving_avg_3
FROM sales;
```

---

### 25. Find Mutual Friends

**Approach:** **Join the friendship table to itself** where both persons A and B are friends with the same person. `f1` finds all friends of person A, `f2` finds all friends of person B, and the join condition `f1.user2_id = f2.user2_id` finds the intersection — people who are friends with both. This is essentially a **set intersection** via SQL.

```sql
-- friendship(user1_id, user2_id)
SELECT f1.user2_id AS mutual_friend
FROM friendships f1
JOIN friendships f2
  ON f1.user2_id = f2.user2_id
WHERE f1.user1_id = :personA
  AND f2.user1_id = :personB;
```

> Assumes bidirectional entries or adjust with UNION.

---

### 26. Unpivot — Columns to Rows

**Approach:** **UNION ALL** — select each column separately with a hardcoded label, then stack them vertically. Each SELECT produces rows for one quarter. `UNION ALL` (not `UNION`) preserves duplicates and avoids a costly sort. Oracle's `UNPIVOT` keyword does this natively — cleaner syntax, same result. This is the reverse of the PIVOT operation.

```sql
SELECT product, 'Q1' AS quarter, q1 AS amount FROM quarterly_sales
UNION ALL
SELECT product, 'Q2', q2 FROM quarterly_sales
UNION ALL
SELECT product, 'Q3', q3 FROM quarterly_sales
UNION ALL
SELECT product, 'Q4', q4 FROM quarterly_sales
ORDER BY product, quarter;
```

**Oracle UNPIVOT:**
```sql
SELECT *
FROM quarterly_sales
UNPIVOT (amount FOR quarter IN (q1 AS 'Q1', q2 AS 'Q2', q3 AS 'Q3', q4 AS 'Q4'));
```

---

### 27. Window Frame — First & Last Value Per Group

**Approach:** `FIRST_VALUE()` and `LAST_VALUE()` are window functions that return the first/last value in the ordered partition. **Critical gotcha:** `LAST_VALUE` with the default frame (`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`) only sees up to the current row, so it doesn't actually return the last value. You **must** specify `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` to see the entire partition.

```sql
SELECT department_id, name, salary,
       FIRST_VALUE(name) OVER (PARTITION BY department_id ORDER BY salary DESC) AS highest_earner,
       LAST_VALUE(name)  OVER (PARTITION BY department_id ORDER BY salary DESC
                               ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS lowest_earner
FROM employees;
```

---

### 28. Detect Fraud — Users With Transactions in Multiple Cities Within 1 Hour

**Approach:** **Self-join on same user, different transaction, different city**, with a time difference ≤ 3600 seconds (1 hour). `ABS(EXTRACT(EPOCH FROM ...))` converts the timestamp difference to seconds. `t1.id <> t2.id` ensures we don't compare a transaction with itself. `DISTINCT` avoids listing a user multiple times if they have many flagged pairs.

```sql
SELECT DISTINCT t1.user_id
FROM transactions t1
JOIN transactions t2
  ON t1.user_id = t2.user_id
  AND t1.id <> t2.id
  AND t1.city <> t2.city
  AND ABS(EXTRACT(EPOCH FROM (t1.txn_time - t2.txn_time))) <= 3600;
```

---

### 29. Island Problem — Find Contiguous Active Periods

**Approach:** Classic **Islands and Gaps** technique. Filter only 'active' rows, then assign `ROW_NUMBER()` per user. Subtract the row number (as days) from the date — consecutive dates produce the **same difference** (the "island" identifier). Group by this computed value to find each contiguous block's start, end, and length. This is one of the most powerful SQL patterns for time-series analysis.

```sql
WITH ranked AS (
    SELECT *,
           event_date - ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY event_date) * INTERVAL '1 day' AS island
    FROM user_activity
    WHERE status = 'active'
)
SELECT user_id,
       MIN(event_date) AS start_date,
       MAX(event_date) AS end_date,
       COUNT(*) AS active_days
FROM ranked
GROUP BY user_id, island
ORDER BY user_id, start_date;
```

---

### 30. Dynamic SQL — Generate Column List from Metadata (Advanced PL/SQL)

**Approach:** Query the **data dictionary** (`user_tab_columns` in Oracle / `information_schema.columns` in MySQL) to get all column names for a table. Concatenate them into a comma-separated string using `LISTAGG` (Oracle) or `GROUP_CONCAT` (MySQL). Build the SQL string dynamically and execute it with `EXECUTE IMMEDIATE` (Oracle) or `PREPARE/EXECUTE` (MySQL). This is used for generic reporting tools or admin scripts.

> Build a SELECT dynamically from all columns in a table.

```sql
-- Oracle PL/SQL
DECLARE
    v_sql   VARCHAR2(4000);
    v_cols  VARCHAR2(4000);
BEGIN
    SELECT LISTAGG(column_name, ', ') WITHIN GROUP (ORDER BY column_id)
    INTO v_cols
    FROM user_tab_columns
    WHERE table_name = 'EMPLOYEES';

    v_sql := 'SELECT ' || v_cols || ' FROM EMPLOYEES WHERE ROWNUM <= 10';
    DBMS_OUTPUT.PUT_LINE(v_sql);
    EXECUTE IMMEDIATE v_sql;
END;
/
```

**MySQL Equivalent (Prepared Statement):**
```sql
SET @cols = NULL;
SELECT GROUP_CONCAT(column_name ORDER BY ordinal_position SEPARATOR ', ')
INTO @cols
FROM information_schema.columns
WHERE table_schema = DATABASE() AND table_name = 'employees';

SET @sql = CONCAT('SELECT ', @cols, ' FROM employees LIMIT 10');
PREPARE stmt FROM @sql;
EXECUTE stmt;
DEALLOCATE PREPARE stmt;
```

---

## 📝 Quick Reference — Key SQL Concepts

| Concept | Syntax |
|---|---|
| **Window Functions** | `OVER (PARTITION BY ... ORDER BY ...)` |
| **Ranking** | `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()` |
| **Offset** | `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()` |
| **Aggregation** | `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()` + `GROUP BY` / `HAVING` |
| **CTE** | `WITH cte AS (...)` |
| **Recursive CTE** | `WITH RECURSIVE cte AS (anchor UNION ALL recursive)` |
| **Self Join** | Join table to itself with alias |
| **Subquery vs CTE** | CTE is more readable; subquery works everywhere |
| **CASE WHEN** | `CASE WHEN cond THEN val ELSE val END` |
| **COALESCE** | Returns first non-null value |

---
