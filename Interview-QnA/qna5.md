# SQL Interview Questions 149-173 Answers

These answers are written in a simple interview style so they are easy to revise and speak out loud.

Assumptions used:

- Table names like `employee` and `department` are sample names.
- Column names may vary in your schema, so adjust them in interviews if needed.
- For paging queries, both generic SQL and common alternatives are mentioned where useful.

---

## Core Concepts

### 149. What is the difference between Primary Key and Unique Key? Can a Primary Key store null?

A `Primary Key` uniquely identifies each row in a table. It cannot store `NULL`, and there can be only one primary key in a table.

A `Unique Key` also prevents duplicate values, but it can usually allow `NULL` depending on the database, and a table can have multiple unique keys.

So the main difference is that primary key is the main identifier of the row and it cannot be null.

### 150. What is a Foreign Key?

A `Foreign Key` is a column or group of columns in one table that refers to the primary key of another table. It is used to maintain relationship and referential integrity between tables.

For example, `dept_id` in an `employee` table can be a foreign key that refers to `dept_id` in the `department` table.

### 151. What is the difference between DDL and DML commands? Give examples.

`DDL` means Data Definition Language. It is used to define or change database structure.

Examples:

- `CREATE`
- `ALTER`
- `DROP`
- `TRUNCATE`

`DML` means Data Manipulation Language. It is used to work with the data inside tables.

Examples:

- `INSERT`
- `UPDATE`
- `DELETE`
- `SELECT`

So DDL changes structure, while DML works on records.

### 152. What are the types of joins in SQL?

The main types of joins are:

- `INNER JOIN`
- `LEFT JOIN`
- `RIGHT JOIN`
- `FULL OUTER JOIN`
- `CROSS JOIN`
- `SELF JOIN`

`INNER JOIN` returns matching rows. `LEFT JOIN` returns all rows from the left table and matched rows from the right. `RIGHT JOIN` is the opposite. `FULL OUTER JOIN` returns matching and non-matching rows from both sides.

### 153. Write the command for an Inner Join.

```sql
SELECT e.emp_id, e.emp_name, d.dept_name
FROM employee e
INNER JOIN department d
ON e.dept_id = d.dept_id;
```

### 154. What is a subquery? What is the command for it?

A subquery is a query written inside another query. It is used when the result of one query is needed by another query.

Example:

```sql
SELECT emp_name, salary
FROM employee
WHERE salary > (
    SELECT AVG(salary)
    FROM employee
);
```

Here, the inner query finds the average salary, and the outer query returns employees whose salary is above that value.

### 155. What is GROUP BY? What is HAVING? What is the difference between them?

`GROUP BY` is used to group rows that have the same values in a column, usually for aggregate functions like `COUNT`, `SUM`, `AVG`, `MAX`, and `MIN`.

`HAVING` is used to filter grouped data after aggregation.

Difference:

- `GROUP BY` creates groups
- `HAVING` filters those groups

Example:

```sql
SELECT dept_id, COUNT(*) AS total_employees
FROM employee
GROUP BY dept_id
HAVING COUNT(*) > 5;
```

### 156. What is the difference between WHERE and HAVING?

`WHERE` filters rows before grouping and aggregation.

`HAVING` filters groups after grouping and aggregation.

Example:

- `WHERE salary > 50000` filters individual rows
- `HAVING COUNT(*) > 3` filters grouped results

### 157. What is normalization? Explain 1NF, 2NF, 3NF.

Normalization is the process of organizing data to reduce redundancy and improve consistency.

`1NF`:

- Each column should contain atomic values
- No repeating groups

`2NF`:

- Table should be in 1NF
- No partial dependency on part of a composite key

`3NF`:

- Table should be in 2NF
- No transitive dependency
- Non-key columns should depend only on the primary key

### 158. What is a View in SQL? Why is it used?

A `View` is a virtual table based on the result of a SQL query. It does not usually store data separately; it shows data from underlying tables.

It is used for:

- simplifying complex queries
- hiding sensitive columns
- improving readability
- reusing logic

Example:

```sql
CREATE VIEW emp_hr_view AS
SELECT emp_id, emp_name, dept_id
FROM employee
WHERE dept_id = 10;
```

### 159. What are integrity constraints?

Integrity constraints are rules applied on table columns to maintain accurate and valid data.

Common constraints are:

- `PRIMARY KEY`
- `FOREIGN KEY`
- `UNIQUE`
- `NOT NULL`
- `CHECK`
- `DEFAULT`

These help keep the data correct and consistent.

### 160. What is pattern matching in SQL? Give an example.

Pattern matching is used to search values based on a pattern. It is usually done using the `LIKE` operator with wildcards.

- `%` means any number of characters
- `_` means exactly one character

Example:

```sql
SELECT emp_name
FROM employee
WHERE emp_name LIKE 'R%';
```

This returns employee names that start with `R`.

### 161. What is PL/SQL? How is it different from SQL?

`PL/SQL` stands for Procedural Language/SQL. It is Oracle’s procedural extension of SQL.

Difference:

- `SQL` is declarative and used to query or manipulate data
- `PL/SQL` supports procedural features like variables, loops, conditions, cursors, and exception handling

So SQL is mainly for database operations, while PL/SQL is used to write program logic around SQL.

---

## SQL Queries

### 162. Write a query to find the second highest salary.

```sql
SELECT MAX(salary) AS second_highest_salary
FROM employee
WHERE salary < (
    SELECT MAX(salary)
    FROM employee
);
```

### 163. Write a query to find the second highest salary using DENSE_RANK.

```sql
SELECT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
) t
WHERE rnk = 2;
```

### 164. Write a query to retrieve the top 10 records from a table.

Generic SQL:

```sql
SELECT *
FROM employee
FETCH FIRST 10 ROWS ONLY;
```

MySQL/PostgreSQL style:

```sql
SELECT *
FROM employee
LIMIT 10;
```

### 165. Write a query to retrieve records 11 to 20 from a table.

Using `ROW_NUMBER()`:

```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (ORDER BY emp_id) AS rn
    FROM employee e
) t
WHERE rn BETWEEN 11 AND 20;
```

MySQL/PostgreSQL style:

```sql
SELECT *
FROM employee
ORDER BY emp_id
LIMIT 10 OFFSET 10;
```

### 166. Write a query to find employee names that start with 'R'.

```sql
SELECT emp_name
FROM employee
WHERE emp_name LIKE 'R%';
```

### 167. Write a query to find duplicate email addresses in a company.

```sql
SELECT email, COUNT(*) AS total
FROM employee
GROUP BY email
HAVING COUNT(*) > 1;
```

### 168. Write a query to join two tables and get the department name for each employee.

```sql
SELECT e.emp_name, d.dept_name
FROM employee e
JOIN department d
ON e.dept_id = d.dept_id;
```

### 169. Write a query to add a new column to an existing table.

```sql
ALTER TABLE employee
ADD phone_number VARCHAR(20);
```

### 170. Write a query using GROUP BY and HAVING.

```sql
SELECT dept_id, COUNT(*) AS total_employees
FROM employee
GROUP BY dept_id
HAVING COUNT(*) >= 2;
```

### 171. Write a query to find the count of employees in each department.

```sql
SELECT dept_id, COUNT(*) AS employee_count
FROM employee
GROUP BY dept_id;
```

If department name is needed:

```sql
SELECT d.dept_name, COUNT(e.emp_id) AS employee_count
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id
GROUP BY d.dept_name;
```

### 172. Write a query to join two tables where two different rows act as primary keys.

If the meaning is that each table has its own primary key and one table references the other through a foreign key, then the join is done on the related key columns.

Example:

```sql
SELECT e.emp_id, e.emp_name, d.dept_id, d.dept_name
FROM employee e
JOIN department d
ON e.dept_id = d.dept_id;
```

If the interviewer means joining on multiple key columns, then it can look like this:

```sql
SELECT *
FROM table1 t1
JOIN table2 t2
ON t1.key1 = t2.key1
AND t1.key2 = t2.key2;
```

### 173. Write a query to find unique values and display department, HR where employee name is Raj.

If the requirement is to display distinct department values for employee name `Raj`, and specifically where department is `HR`, then:

```sql
SELECT DISTINCT department
FROM employee
WHERE emp_name = 'Raj'
AND department = 'HR';
```

If the requirement is simply to display department and name where the employee is Raj from HR:

```sql
SELECT DISTINCT department, emp_name
FROM employee
WHERE emp_name = 'Raj'
AND department = 'HR';
```
