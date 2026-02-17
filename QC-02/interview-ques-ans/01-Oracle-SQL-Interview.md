# Oracle SQL — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. The answers are written in the way you should speak in an interview — clear, confident, and concise.

---

## 1. What is RDBMS? How is it different from DBMS?

**Answer:**
A **Relational Database Management System** stores data in **tables with rows and columns**, and tables can be related to each other using **foreign keys**. A regular DBMS (like a flat file system) stores data without any defined relationships.

The key difference is that RDBMS enforces **ACID properties** — Atomicity, Consistency, Isolation, and Durability — which guarantee reliable transactions. For example, if you're transferring money between two accounts, ACID ensures either both the debit and credit happen, or neither does. A plain DBMS doesn't guarantee that.

Examples of RDBMS: Oracle, MySQL, PostgreSQL, SQL Server.

---

## 2. What is the difference between SQL and NoSQL?

**Answer:**
**SQL databases** are relational — they use structured tables with a fixed schema, support joins, and enforce ACID transactions. They're best when your data is structured and relationships matter, like banking systems or ERP.

**NoSQL databases** are non-relational — they can store data as documents (MongoDB), key-value pairs (Redis), wide-column (Cassandra), or graphs (Neo4j). They're schema-flexible, horizontally scalable, and follow BASE (Basically Available, Soft state, Eventually consistent) rather than ACID.

I'd choose SQL when data integrity and complex queries are critical, and NoSQL when I need high scalability with flexible/unstructured data — like social media feeds or real-time analytics.

---

## 3. What are the different SQL sublanguages?

**Answer:**
SQL has five sublanguages:
- **DDL** (Data Definition Language) — defines structure: `CREATE`, `ALTER`, `DROP`, `TRUNCATE`
- **DML** (Data Manipulation Language) — manipulates data: `INSERT`, `UPDATE`, `DELETE`
- **DQL** (Data Query Language) — queries data: `SELECT`
- **DCL** (Data Control Language) — controls access: `GRANT`, `REVOKE`
- **TCL** (Transaction Control Language) — manages transactions: `COMMIT`, `ROLLBACK`, `SAVEPOINT`

---

## 4. What is the difference between DELETE, TRUNCATE, and DROP?

**Answer:**
- **DELETE** is DML — removes specific rows, can have a WHERE clause, can be rolled back, fires triggers, and is slower because it logs each row deletion.
- **TRUNCATE** is DDL — removes ALL rows, cannot have a WHERE clause, cannot be rolled back (auto-commits), does NOT fire triggers, and is faster because it deallocates data pages.
- **DROP** is DDL — removes the entire table structure along with all data, indexes, and constraints. The table no longer exists.

In short: DELETE removes rows, TRUNCATE empties the table, DROP destroys the table.

---

## 5. What are constraints in SQL? Name them.

**Answer:**
Constraints are rules enforced on table columns to maintain data integrity:

- **PRIMARY KEY** — uniquely identifies each row. Combination of NOT NULL + UNIQUE. Only one per table.
- **FOREIGN KEY** — references a primary key in another table, enforcing referential integrity.
- **UNIQUE** — ensures all values in a column are distinct. Allows one NULL.
- **NOT NULL** — column cannot have NULL values.
- **CHECK** — ensures values satisfy a condition, like `CHECK (age >= 18)`.
- **DEFAULT** — provides a default value when none is specified during INSERT.

---

## 6. What is the difference between PRIMARY KEY and UNIQUE?

**Answer:**
Both enforce uniqueness, but:
- **PRIMARY KEY** does NOT allow NULL values, and there can be only **one** per table.
- **UNIQUE** allows **one NULL** value, and you can have **multiple** UNIQUE constraints per table.

Under the hood, both create an index, but the primary key creates a **clustered index** (in most databases), while UNIQUE creates a non-clustered index.

---

## 7. What are JOINs? Explain each type.

**Answer:**
JOINs combine rows from two or more tables based on a related column:

- **INNER JOIN** — returns only rows that have matching values in BOTH tables.
- **LEFT JOIN** (LEFT OUTER JOIN) — returns ALL rows from the left table, and matching rows from the right. If no match, right side is NULL.
- **RIGHT JOIN** — returns ALL rows from the right table, and matching rows from the left.
- **FULL OUTER JOIN** — returns ALL rows from both tables. NULLs where there's no match on either side.
- **CROSS JOIN** — returns the Cartesian product — every row from table A paired with every row from table B.
- **SELF JOIN** — a table joined with itself, useful for hierarchical data like employee-manager relationships.

---

## 8. What is the difference between WHERE and HAVING?

**Answer:**
- **WHERE** filters rows **before** grouping. It works on individual rows and cannot use aggregate functions.
- **HAVING** filters groups **after** GROUP BY. It works on grouped results and CAN use aggregate functions.

```sql
-- WHERE filters rows before grouping
SELECT department_id, AVG(salary)
FROM employees
WHERE salary > 3000          -- Filter individual rows first
GROUP BY department_id
HAVING AVG(salary) > 5000;   -- Then filter groups
```

---

## 9. What is a subquery? What types are there?

**Answer:**
A subquery is a query nested inside another query. Types include:

- **Single-row subquery** — returns one value: `WHERE salary > (SELECT AVG(salary) FROM employees)`
- **Multi-row subquery** — returns multiple values, used with IN, ANY, ALL: `WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'NYC')`
- **Correlated subquery** — references the outer query and executes once per outer row: `WHERE salary > (SELECT AVG(salary) FROM employees e2 WHERE e2.dept_id = e1.dept_id)`

Correlated subqueries are slower because they execute repeatedly, while regular subqueries execute only once.

---

## 10. What is a View? Why use it?

**Answer:**
A View is a **virtual table** based on a SELECT query. It doesn't store data — it stores the query definition, and the data is fetched from the underlying tables each time you query the view.

Benefits:
- **Security** — expose only certain columns to users
- **Simplicity** — hide complex joins behind a simple view name
- **Consistency** — ensure everyone uses the same query logic
- **Abstraction** — change underlying tables without affecting consumers

```sql
CREATE VIEW active_employees AS
SELECT id, name, department FROM employees WHERE status = 'ACTIVE';
```

---

## 11. What are aggregate functions?

**Answer:**
Aggregate functions perform calculations on a set of values and return a single result:
- `COUNT()` — number of rows
- `SUM()` — total of values
- `AVG()` — average
- `MIN()` — smallest value
- `MAX()` — largest value

They're typically used with `GROUP BY`. Important: aggregate functions ignore NULL values, except `COUNT(*)` which counts all rows including NULLs.

---

## 12. What is the difference between UNION and UNION ALL?

**Answer:**
Both combine results from two SELECT statements:
- **UNION** removes duplicate rows (performs a sort to eliminate duplicates, so it's slower).
- **UNION ALL** keeps all rows including duplicates (faster because no sorting).

If you know there won't be duplicates, always use UNION ALL for better performance.

---

## 13. What is the order of execution of a SQL query?

**Answer:**
SQL does NOT execute in the order you write it. The actual execution order is:

1. **FROM** — identify the tables
2. **JOIN** — combine tables
3. **WHERE** — filter rows
4. **GROUP BY** — group rows
5. **HAVING** — filter groups
6. **SELECT** — choose columns
7. **DISTINCT** — remove duplicates
8. **ORDER BY** — sort results
9. **LIMIT/OFFSET** — limit rows returned

This is why you can't use a column alias defined in SELECT inside the WHERE clause — WHERE executes before SELECT.

---

## 14. What is normalization? What are the normal forms?

**Answer:**
Normalization is the process of organizing data to reduce redundancy and improve integrity.

- **1NF** — each column has atomic (single) values, no repeating groups.
- **2NF** — 1NF + all non-key columns fully depend on the ENTIRE primary key (no partial dependencies).
- **3NF** — 2NF + no column depends on another non-key column (no transitive dependencies).

For example, if a table has `student_id, course_id, student_name`, the `student_name` depends only on `student_id` (partial dependency on composite key), violating 2NF.

---

## 15. What is an Index? When would you use one?

**Answer:**
An index is a data structure (usually a B-tree) that speeds up data retrieval. Think of it like a book's index — instead of reading every page, you look up the keyword and jump to the right page.

Use indexes on columns that appear in:
- WHERE clauses
- JOIN conditions
- ORDER BY / GROUP BY

Don't over-index — indexes speed up reads but slow down writes (INSERT/UPDATE/DELETE), because the index must be updated too.

---

*Back to: [01-Oracle-SQL-Fundamentals.md](../01-Oracle-SQL-Fundamentals.md)*
