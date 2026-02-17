# Oracle SQL Fundamentals — From Zero to Expert

## Table of Contents
1. [Introduction to RDBMS](#1-introduction-to-rdbms)
2. [SQL vs NoSQL](#2-sql-vs-nosql)
3. [Data Types in Oracle](#3-data-types-in-oracle)
4. [Structuring Data — Tables & Schema Design](#4-structuring-data)
5. [DDL — CREATE, ALTER, DROP, TRUNCATE](#5-ddl-commands)
6. [Constraints](#6-constraints)
7. [DML — INSERT, UPDATE, DELETE](#7-dml-commands)
8. [SELECT & WHERE Clause](#8-select-and-where)
9. [Sorting & Filtering Data](#9-sorting-and-filtering)
10. [Operators — IN, BETWEEN, IS NULL, AND, OR, NOT](#10-operators)
11. [Functions — Aggregate, String, Date, Numeric, Conversion](#11-functions)
12. [GROUP BY & HAVING](#12-group-by-and-having)
13. [Joins — All Types](#13-joins)
14. [Subqueries](#14-subqueries)
15. [Set Operations — UNION, INTERSECT, MINUS](#15-set-operations)
16. [Views](#16-views)

---

## 1. Introduction to RDBMS

### What Is a Database?
A **database** is simply an organized collection of data. Think of it like a filing cabinet — each drawer holds a category of information, each folder holds related records.

### What Is an RDBMS?
**RDBMS** = **Relational Database Management System**

It stores data in **tables** (also called **relations**). Each table has **rows** (records) and **columns** (fields/attributes).

```
┌──────────────────────────────────────────┐
│             EMPLOYEES TABLE              │
├──────┬───────────┬──────────┬────────────┤
│  id  │   name    │   dept   │   salary   │
├──────┼───────────┼──────────┼────────────┤
│  1   │  Alice    │  Sales   │   50000    │
│  2   │  Bob      │  IT      │   65000    │
│  3   │  Charlie  │  Sales   │   52000    │
└──────┴───────────┴──────────┴────────────┘
```

### How RDBMS Works Under the Hood

When you run a SQL query, here's what happens internally:

```
Your SQL Query
     │
     ▼
┌─────────────┐
│   Parser     │  ← Checks syntax, validates table/column names
├─────────────┤
│  Optimizer   │  ← Finds the best execution plan (which index to use, etc.)
├─────────────┤
│  Executor    │  ← Actually runs the plan
├─────────────┤
│ Storage Eng. │  ← Reads/writes data from/to disk (data files, redo logs)
└─────────────┘
```

**Key Concepts:**
- **Schema**: The blueprint/structure of your database (table definitions, relationships).
- **Instance**: A running copy of the database engine in memory.
- **Tablespace**: Logical storage units where Oracle stores table data physically on disk.
- **Data Files**: Actual `.dbf` files on disk that hold your data.
- **Redo Logs**: Files that record every change — used for crash recovery.
- **Buffer Cache**: Data is read from disk into RAM (buffer cache) for fast access.

### ACID Properties — Why RDBMS Is Reliable

Every RDBMS transaction follows ACID:

| Property | Meaning | Example |
|---|---|---|
| **Atomicity** | All or nothing. Either the whole transaction completes or none of it does. | Transferring money: debit AND credit must both happen or neither. |
| **Consistency** | Database moves from one valid state to another. Constraints are never violated. | A foreign key always points to an existing row. |
| **Isolation** | Concurrent transactions don't interfere with each other. | Two users updating the same row — one waits for the other. |
| **Durability** | Once committed, data survives crashes. | After COMMIT, even if power fails, your data is safe (redo logs). |

### E.F. Codd's 12 Rules (Simplified)
Edgar F. Codd invented the relational model. His rules define what makes a "true" RDBMS:

1. **Information Rule** — All data is stored in tables (rows & columns).
2. **Guaranteed Access** — Every piece of data is reachable by table name + column name + primary key.
3. **Null Values** — NULLs represent missing/unknown data distinctly from empty or zero.
4. **Active Online Catalog** — The database's structure is itself stored in tables (metadata).
5. **Comprehensive Data Sublanguage** — The system must support a language like SQL.
6. **View Updating** — Views (virtual tables) should be updatable where theoretically possible.
7. **Set-Level Operations** — INSERT, UPDATE, DELETE should work on sets of rows, not just one row.
8. **Physical Data Independence** — Changing how data is stored on disk shouldn't break your queries.
9. **Logical Data Independence** — Changing table structure shouldn't break existing applications (where possible).
10. **Integrity Independence** — Integrity constraints are stored in the catalog, not in application code.
11. **Distribution Independence** — If the database is distributed across servers, the user shouldn't notice.
12. **Non-Subversion** — If the system provides a record-at-a-time interface, it can't bypass security rules.

---

## 2. SQL vs NoSQL

### SQL (Relational) Databases
Examples: Oracle, MySQL, PostgreSQL, SQL Server

```
┌──────────────────────────────────────┐
│  Structured data in rigid tables     │
│  Fixed schema (must define columns)  │
│  ACID compliant                      │
│  Vertical scaling (bigger machine)   │
│  Best for: banking, ERP, inventory   │
└──────────────────────────────────────┘
```

### NoSQL Databases
Examples: MongoDB (document), Redis (key-value), Cassandra (column), Neo4j (graph)

```
┌──────────────────────────────────────────┐
│  Flexible schema (schema-less)           │
│  Horizontal scaling (add more machines)  │
│  BASE instead of ACID (Eventually        │
│    Consistent)                           │
│  Best for: social media, real-time apps, │
│    IoT, big data                         │
└──────────────────────────────────────────┘
```

### Side-by-Side Comparison

| Feature | SQL | NoSQL |
|---|---|---|
| Data Model | Tables with rows & columns | Documents, Key-Value, Graph, Column |
| Schema | Fixed (must define before insert) | Dynamic (can change anytime) |
| Scalability | Vertical (scale up) | Horizontal (scale out) |
| Transactions | Strong ACID | Eventual consistency (usually) |
| Query Language | SQL (standardized) | Varies per database |
| Joins | Powerful multi-table joins | Usually no joins (denormalized data) |
| Best Use Case | Complex queries, relationships | Large-scale, flexible, fast reads |

### When to Use What?
- **Use SQL** when: data has clear relationships, you need transactions (banking), data integrity matters.
- **Use NoSQL** when: data structure varies, you need massive scale, speed over consistency.

---

## 3. Data Types in Oracle

Data types tell Oracle what kind of data a column can hold and how much space to allocate.

### Numeric Data Types

```sql
-- NUMBER(precision, scale)
-- precision = total digits, scale = digits after decimal point

salary    NUMBER(10, 2)    -- up to 99999999.99
age       NUMBER(3)        -- up to 999 (integer, no decimals)
quantity  NUMBER           -- up to 38 digits (maximum precision)

-- Under the hood: Oracle stores numbers in a variable-length internal format
-- It doesn't waste space — NUMBER(3) doesn't use the same space as NUMBER(38)
```

| Type | Description | Example |
|---|---|---|
| `NUMBER(p,s)` | Decimal number, p = precision, s = scale | `NUMBER(8,2)` → 123456.78 |
| `INTEGER` | Alias for `NUMBER(38,0)` | Whole numbers |
| `FLOAT(b)` | Floating point with b binary digits | `FLOAT(126)` |
| `BINARY_FLOAT` | 32-bit IEEE floating point | Fast math, approximate |
| `BINARY_DOUBLE` | 64-bit IEEE floating point | Fast math, approximate |

### Character Data Types

```sql
-- VARCHAR2(size) — Variable length string (USE THIS MOST!)
name      VARCHAR2(100)    -- Up to 100 characters, stores only what you give it

-- CHAR(size) — Fixed length string (pads with spaces!)
gender    CHAR(1)          -- Always stores exactly 1 character
code      CHAR(5)          -- 'AB' is stored as 'AB   ' (padded to 5)

-- NVARCHAR2 / NCHAR — For Unicode (multi-language support)
name_jp   NVARCHAR2(100)   -- Can store Japanese, Chinese, etc.

-- CLOB — Character Large Object (for huge text)
essay     CLOB             -- Up to 4 GB of text!
```

**Under the Hood:**
- `VARCHAR2` stores the actual data + a length byte. No wasted space.
- `CHAR` always allocates the full size. `CHAR(100)` always uses ~100 bytes, even for 'Hi'.
- **Always prefer VARCHAR2 over CHAR** unless you truly have fixed-length data.

### Date & Time Data Types

```sql
-- DATE — stores date AND time (to the second)
hire_date    DATE              -- e.g., 2025-03-15 14:30:00

-- TIMESTAMP — like DATE but with fractional seconds
created_at   TIMESTAMP         -- e.g., 2025-03-15 14:30:00.123456

-- TIMESTAMP WITH TIME ZONE
event_time   TIMESTAMP WITH TIME ZONE  -- stores timezone info too

-- INTERVAL
duration     INTERVAL YEAR TO MONTH     -- e.g., '2-3' = 2 years 3 months
elapsed      INTERVAL DAY TO SECOND     -- e.g., '5 12:30:00' = 5 days 12h 30m
```

**Under the Hood:**
- Oracle stores DATE as 7 bytes internally: century, year, month, day, hour, minute, second.
- TIMESTAMP uses 7–11 bytes (extra for fractional seconds).

### Large Object Data Types

```sql
CLOB      -- Character Large Object (text up to 4 GB)
BLOB      -- Binary Large Object (images, files up to 4 GB)
BFILE     -- Pointer to a file on the server's filesystem
```

### Data Type Best Practices
1. Use `VARCHAR2` for strings (not `CHAR`).
2. Use `NUMBER` for exact calculations (money, quantities).
3. Use `DATE` or `TIMESTAMP` for dates (never store dates as strings!).
4. Use `CLOB` only when data exceeds VARCHAR2 limit (4000 bytes in standard SQL, 32767 in PL/SQL).

---

## 4. Structuring Data

### Normalization — Organizing Data to Remove Redundancy

**Un-normalized (bad):**
```
┌───────────────────────────────────────────────────────────┐
│ order_id │ customer │ cust_phone │ product │ product_price │
├──────────┼──────────┼────────────┼─────────┼───────────────┤
│    1     │  Alice   │ 555-1234   │  Laptop │     999       │
│    2     │  Alice   │ 555-1234   │  Mouse  │      25       │  ← Alice's phone repeated!
│    3     │  Bob     │ 555-5678   │  Laptop │     999       │  ← Laptop price repeated!
└──────────┴──────────┴────────────┴─────────┴───────────────┘
Problem: If Alice changes her phone, you must update EVERY row. Miss one? Data corruption.
```

**1NF (First Normal Form):** Each cell holds a single value, each row is unique.
**2NF (Second Normal Form):** Every non-key column depends on the entire primary key.
**3NF (Third Normal Form):** No column depends on another non-key column (no transitive dependency).

**Normalized (good):**
```
CUSTOMERS                    PRODUCTS                 ORDERS
┌────┬───────┬──────────┐   ┌────┬────────┬───────┐  ┌────┬─────────┬────────────┐
│ id │ name  │  phone   │   │ id │  name  │ price │  │ id │ cust_id │ product_id │
├────┼───────┼──────────┤   ├────┼────────┼───────┤  ├────┼─────────┼────────────┤
│  1 │ Alice │ 555-1234 │   │  1 │ Laptop │  999  │  │  1 │    1    │     1      │
│  2 │ Bob   │ 555-5678 │   │  2 │ Mouse  │   25  │  │  2 │    1    │     2      │
└────┴───────┴──────────┘   └────┴────────┴───────┘  │  3 │    2    │     1      │
                                                      └────┴─────────┴────────────┘
Now: Change Alice's phone in ONE place. Laptop price in ONE place.
```

---

## 5. DDL Commands

**DDL** = **Data Definition Language** — Commands that define/modify the structure of database objects.

### CREATE TABLE

```sql
-- Basic syntax
CREATE TABLE employees (
    emp_id      NUMBER(6)      PRIMARY KEY,
    first_name  VARCHAR2(50)   NOT NULL,
    last_name   VARCHAR2(50)   NOT NULL,
    email       VARCHAR2(100)  UNIQUE,
    hire_date   DATE           DEFAULT SYSDATE,
    salary      NUMBER(10,2)   CHECK (salary > 0),
    dept_id     NUMBER(4)      REFERENCES departments(dept_id)
);

-- What happens under the hood:
-- 1. Oracle allocates a SEGMENT in a tablespace for this table
-- 2. The segment is divided into EXTENTS (contiguous blocks)
-- 3. Each extent has DATA BLOCKS (usually 8KB each)
-- 4. Metadata is recorded in the DATA DICTIONARY (system tables)
-- 5. You can query: SELECT * FROM user_tables WHERE table_name = 'EMPLOYEES';
```

### ALTER TABLE

```sql
-- Add a column
ALTER TABLE employees ADD (phone VARCHAR2(20));

-- Modify a column's data type or size
ALTER TABLE employees MODIFY (phone VARCHAR2(30));

-- Rename a column
ALTER TABLE employees RENAME COLUMN phone TO phone_number;

-- Drop a column
ALTER TABLE employees DROP COLUMN phone_number;

-- Add a constraint after creation
ALTER TABLE employees ADD CONSTRAINT chk_salary CHECK (salary >= 0);

-- Disable/Enable a constraint
ALTER TABLE employees DISABLE CONSTRAINT chk_salary;
ALTER TABLE employees ENABLE CONSTRAINT chk_salary;
```

### DROP TABLE

```sql
-- Drop table (moves to recycle bin by default in Oracle)
DROP TABLE employees;

-- Drop permanently (bypass recycle bin)
DROP TABLE employees PURGE;

-- Drop even if other tables reference it (cascades foreign key constraints)
DROP TABLE departments CASCADE CONSTRAINTS;

-- Recover from recycle bin
FLASHBACK TABLE employees TO BEFORE DROP;
```

### TRUNCATE TABLE

```sql
-- Remove ALL rows, but keep the table structure
TRUNCATE TABLE employees;
```

### DROP vs TRUNCATE vs DELETE — Key Differences

| Feature | DROP | TRUNCATE | DELETE |
|---|---|---|---|
| What it removes | Table + data + structure | All rows (keeps structure) | Specific rows |
| DDL or DML? | DDL | DDL | DML |
| Rollback? | ❌ No (auto-commit) | ❌ No (auto-commit) | ✅ Yes (can rollback) |
| WHERE clause? | ❌ No | ❌ No | ✅ Yes |
| Speed | Fast | Very fast | Slow (row-by-row) |
| Space freed? | Yes | Yes (resets high water mark) | No (until manual shrink) |
| Triggers fire? | No | No | Yes |

**Under the Hood:**
- `DELETE` generates undo data for each row (so you can ROLLBACK). This is slow for millions of rows.
- `TRUNCATE` simply resets the table's **High Water Mark** (the pointer to where data ends) — almost instant.
- `DROP` removes the table from the data dictionary and deallocates the segment.

---

## 6. Constraints

Constraints are rules that enforce data integrity. They prevent bad data from entering your database.

### PRIMARY KEY

```sql
-- Uniquely identifies each row. Cannot be NULL. Only ONE per table.
CREATE TABLE departments (
    dept_id    NUMBER(4)    PRIMARY KEY,
    dept_name  VARCHAR2(50) NOT NULL
);

-- Composite primary key (multiple columns together form the key)
CREATE TABLE enrollments (
    student_id  NUMBER(6),
    course_id   NUMBER(6),
    enroll_date DATE,
    PRIMARY KEY (student_id, course_id)  -- combination must be unique
);
```

**Under the Hood:** Oracle automatically creates a **UNIQUE INDEX** for the primary key. This is a B-Tree index that allows fast lookups — O(log n) instead of O(n).

### FOREIGN KEY

```sql
-- References a primary key in another table. Enforces relationships.
CREATE TABLE employees (
    emp_id    NUMBER(6)    PRIMARY KEY,
    name      VARCHAR2(50),
    dept_id   NUMBER(4)    REFERENCES departments(dept_id)
    -- dept_id MUST exist in departments table!
);

-- With explicit constraint name and actions:
CREATE TABLE employees (
    emp_id    NUMBER(6)    PRIMARY KEY,
    name      VARCHAR2(50),
    dept_id   NUMBER(4),
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) 
        REFERENCES departments(dept_id)
        ON DELETE CASCADE    -- If department is deleted, delete all its employees
        -- ON DELETE SET NULL -- Alternative: set dept_id to NULL instead
);
```

**Referential Actions:**
- `ON DELETE CASCADE` — Deleting a parent row deletes all child rows.
- `ON DELETE SET NULL` — Deleting a parent row sets child FK to NULL.
- Default (no action) — Prevents deletion if children exist.

### OTHER CONSTRAINTS

```sql
-- UNIQUE — No duplicate values (but allows multiple NULLs)
email VARCHAR2(100) UNIQUE,

-- NOT NULL — Column cannot be NULL
name VARCHAR2(50) NOT NULL,

-- CHECK — Custom validation rule
salary NUMBER(10,2) CHECK (salary > 0),
age NUMBER(3) CHECK (age BETWEEN 18 AND 65),
status VARCHAR2(10) CHECK (status IN ('ACTIVE', 'INACTIVE')),

-- DEFAULT — Automatic value if none provided
hire_date DATE DEFAULT SYSDATE,
status VARCHAR2(10) DEFAULT 'ACTIVE'
```

---

## 7. DML Commands

**DML** = **Data Manipulation Language** — Commands that manipulate the data inside tables.

### INSERT

```sql
-- Insert a single row (specify all columns)
INSERT INTO employees (emp_id, first_name, last_name, salary, hire_date)
VALUES (1, 'Alice', 'Smith', 75000, TO_DATE('2024-01-15', 'YYYY-MM-DD'));

-- Insert without column list (must provide ALL columns in order — NOT recommended)
INSERT INTO employees VALUES (2, 'Bob', 'Jones', 'bob@email.com', SYSDATE, 65000, 10);

-- Insert from a SELECT (copy data from another table)
INSERT INTO employees_backup
SELECT * FROM employees WHERE dept_id = 10;

-- Insert multiple rows (Oracle syntax with INSERT ALL)
INSERT ALL
    INTO products (id, name, price) VALUES (1, 'Laptop', 999)
    INTO products (id, name, price) VALUES (2, 'Mouse', 25)
    INTO products (id, name, price) VALUES (3, 'Keyboard', 75)
SELECT 1 FROM DUAL;
```

**Under the Hood:**
1. Oracle finds free space in a data block (below the High Water Mark).
2. The row is written to the block in the **buffer cache** (RAM).
3. The change is recorded in the **redo log buffer** (for crash recovery).
4. The old state is saved in the **undo tablespace** (for rollback).
5. On `COMMIT`, the redo log buffer is flushed to disk. The data blocks are written later by **DBWR** (Database Writer background process).

### UPDATE

```sql
-- Update specific rows
UPDATE employees 
SET salary = salary * 1.10 
WHERE dept_id = 20;

-- Update multiple columns
UPDATE employees 
SET salary = 80000, 
    email = 'alice.new@email.com' 
WHERE emp_id = 1;

-- Update with subquery
UPDATE employees 
SET salary = (SELECT AVG(salary) FROM employees) 
WHERE performance_rating = 'A';
```

### DELETE

```sql
-- Delete specific rows
DELETE FROM employees WHERE emp_id = 5;

-- Delete all rows (slow — use TRUNCATE if you don't need rollback)
DELETE FROM employees;

-- Delete with subquery
DELETE FROM employees 
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'OLD_BUILDING');
```

### COMMIT, ROLLBACK, SAVEPOINT

```sql
-- Start making changes (implicit transaction begins)
INSERT INTO employees VALUES (10, 'Test', 'User', 'test@email.com', SYSDATE, 50000, 10);
UPDATE employees SET salary = 60000 WHERE emp_id = 10;

SAVEPOINT before_delete;  -- Create a savepoint

DELETE FROM employees WHERE emp_id = 3;

ROLLBACK TO before_delete;  -- Undo only the DELETE, keep INSERT and UPDATE

COMMIT;  -- Make INSERT and UPDATE permanent
-- After COMMIT, you CANNOT rollback!
```

---

## 8. SELECT & WHERE Clause

### The SELECT Statement

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT first_name, last_name, salary FROM employees;

-- Column aliases
SELECT first_name AS "First Name", salary * 12 AS annual_salary FROM employees;

-- Expressions in SELECT
SELECT first_name, salary, salary * 0.10 AS bonus, salary + (salary * 0.10) AS total
FROM employees;

-- DISTINCT — remove duplicate rows
SELECT DISTINCT dept_id FROM employees;

-- DUAL — Oracle's dummy table (for calculations without a real table)
SELECT 2 + 3 FROM DUAL;          -- Returns 5
SELECT SYSDATE FROM DUAL;        -- Returns current date
SELECT USER FROM DUAL;           -- Returns current user
```

### The WHERE Clause

```sql
-- Filtering rows
SELECT * FROM employees WHERE salary > 50000;
SELECT * FROM employees WHERE dept_id = 10;
SELECT * FROM employees WHERE hire_date > TO_DATE('2024-01-01', 'YYYY-MM-DD');

-- String comparison (case-sensitive in Oracle!)
SELECT * FROM employees WHERE last_name = 'Smith';
SELECT * FROM employees WHERE UPPER(last_name) = 'SMITH';  -- case-insensitive
```

### Execution Order of a SELECT Statement

This is crucial to understand! SQL doesn't execute top-to-bottom:

```
Written Order:          Execution Order:
1. SELECT               5. SELECT
2. FROM                  1. FROM
3. WHERE                 2. WHERE
4. GROUP BY              3. GROUP BY
5. HAVING                4. HAVING
6. ORDER BY              6. ORDER BY

-- This is why you CAN'T use a column alias in WHERE:
SELECT salary * 12 AS annual FROM employees WHERE annual > 100000;  -- ❌ ERROR!
-- WHERE runs BEFORE SELECT, so 'annual' doesn't exist yet!

SELECT salary * 12 AS annual FROM employees WHERE salary * 12 > 100000;  -- ✅ WORKS
```

---

## 9. Sorting & Filtering Data

### ORDER BY

```sql
-- Sort ascending (default)
SELECT * FROM employees ORDER BY salary;        -- lowest first
SELECT * FROM employees ORDER BY salary ASC;    -- same thing

-- Sort descending
SELECT * FROM employees ORDER BY salary DESC;   -- highest first

-- Multiple columns
SELECT * FROM employees ORDER BY dept_id ASC, salary DESC;
-- First sort by department, then within each dept, highest salary first

-- Sort by column position
SELECT first_name, last_name, salary FROM employees ORDER BY 3 DESC;
-- 3 means the 3rd column in SELECT list (salary)

-- Sort by alias
SELECT first_name, salary * 12 AS annual FROM employees ORDER BY annual DESC;
-- This works because ORDER BY executes AFTER SELECT!

-- NULLs — by default, NULLs sort LAST in ASC, FIRST in DESC
SELECT * FROM employees ORDER BY commission_pct ASC NULLS FIRST;
SELECT * FROM employees ORDER BY commission_pct DESC NULLS LAST;
```

### Filtering with LIKE & Wildcards

```sql
-- % matches zero or more characters
SELECT * FROM employees WHERE last_name LIKE 'S%';       -- starts with S
SELECT * FROM employees WHERE last_name LIKE '%son';      -- ends with 'son'
SELECT * FROM employees WHERE last_name LIKE '%mi%';      -- contains 'mi'

-- _ matches exactly one character
SELECT * FROM employees WHERE last_name LIKE '_a%';       -- second char is 'a'
SELECT * FROM employees WHERE phone LIKE '___-____';      -- pattern: 3 digits-4 digits

-- Escape special characters
SELECT * FROM products WHERE name LIKE '%10\%%' ESCAPE '\';  -- find '10%' literally
```

### DISTINCT

```sql
-- Remove duplicate values
SELECT DISTINCT dept_id FROM employees;

-- DISTINCT across multiple columns (unique combinations)
SELECT DISTINCT dept_id, job_id FROM employees;
-- Returns each unique (dept_id, job_id) pair
```

---

## 10. Operators

### Comparison Operators

```sql
=     -- equal
!=    -- not equal (also <> or ^=)
>     -- greater than
<     -- less than
>=    -- greater than or equal
<=    -- less than or equal
```

### IN Operator

```sql
-- Instead of multiple OR conditions:
SELECT * FROM employees WHERE dept_id = 10 OR dept_id = 20 OR dept_id = 30;

-- Use IN:
SELECT * FROM employees WHERE dept_id IN (10, 20, 30);

-- NOT IN:
SELECT * FROM employees WHERE dept_id NOT IN (10, 20);

-- IN with subquery:
SELECT * FROM employees WHERE dept_id IN (
    SELECT dept_id FROM departments WHERE location_id = 1700
);
```

### BETWEEN Operator

```sql
-- Inclusive range (includes both endpoints)
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 80000;
-- Same as: WHERE salary >= 50000 AND salary <= 80000

-- Works with dates too
SELECT * FROM employees 
WHERE hire_date BETWEEN TO_DATE('2024-01-01', 'YYYY-MM-DD') 
                     AND TO_DATE('2024-12-31', 'YYYY-MM-DD');

-- NOT BETWEEN
SELECT * FROM employees WHERE salary NOT BETWEEN 50000 AND 80000;
```

### IS NULL / IS NOT NULL

```sql
-- NULL is NOT a value — it's the absence of a value
-- You CANNOT use = or != with NULL!

SELECT * FROM employees WHERE commission_pct = NULL;      -- ❌ WRONG! Returns nothing!
SELECT * FROM employees WHERE commission_pct IS NULL;     -- ✅ CORRECT

SELECT * FROM employees WHERE commission_pct IS NOT NULL; -- Has a commission

-- NULL arithmetic: any operation with NULL returns NULL
SELECT 5 + NULL FROM DUAL;    -- Returns NULL
SELECT NULL = NULL FROM DUAL; -- Returns NULL (not TRUE!)
```

### Logical Operators (AND, OR, NOT)

```sql
-- AND — both conditions must be true
SELECT * FROM employees WHERE dept_id = 10 AND salary > 50000;

-- OR — at least one condition must be true
SELECT * FROM employees WHERE dept_id = 10 OR dept_id = 20;

-- NOT — negates a condition
SELECT * FROM employees WHERE NOT (dept_id = 10);
SELECT * FROM employees WHERE dept_id NOT IN (10, 20);

-- Operator precedence: NOT → AND → OR
-- Use parentheses to be explicit!
SELECT * FROM employees 
WHERE (dept_id = 10 OR dept_id = 20) AND salary > 50000;
-- Without parentheses: dept_id = 10 OR (dept_id = 20 AND salary > 50000)  ← Different!
```

---

## 11. Functions

### Aggregate Functions

Aggregate functions operate on **groups of rows** and return a **single value**.

```sql
-- COUNT — number of rows
SELECT COUNT(*) FROM employees;                    -- count all rows (including NULLs)
SELECT COUNT(commission_pct) FROM employees;       -- count non-NULL values only
SELECT COUNT(DISTINCT dept_id) FROM employees;     -- count unique departments

-- SUM — total
SELECT SUM(salary) FROM employees;
SELECT SUM(salary) FROM employees WHERE dept_id = 10;

-- AVG — average
SELECT AVG(salary) FROM employees;
SELECT ROUND(AVG(salary), 2) FROM employees;       -- rounded to 2 decimals

-- MIN / MAX
SELECT MIN(salary), MAX(salary) FROM employees;
SELECT MIN(hire_date), MAX(hire_date) FROM employees;  -- works with dates too!

-- ⚠️ Important: Aggregate functions IGNORE NULL values!
-- If 5 out of 10 employees have NULL commission:
SELECT AVG(commission_pct) FROM employees;  -- Average of the 5 non-NULL values only
SELECT AVG(NVL(commission_pct, 0)) FROM employees;  -- Treat NULL as 0, average of all 10
```

### String Functions

```sql
-- UPPER, LOWER, INITCAP
SELECT UPPER('hello')   FROM DUAL;  -- 'HELLO'
SELECT LOWER('HELLO')   FROM DUAL;  -- 'hello'
SELECT INITCAP('hello world') FROM DUAL;  -- 'Hello World'

-- LENGTH
SELECT LENGTH('Hello') FROM DUAL;  -- 5

-- SUBSTR(string, start_position, length)
SELECT SUBSTR('Hello World', 1, 5)  FROM DUAL;  -- 'Hello'
SELECT SUBSTR('Hello World', 7)     FROM DUAL;  -- 'World'
SELECT SUBSTR('Hello World', -5)    FROM DUAL;  -- 'World' (negative = from end)

-- INSTR(string, search_string, start, occurrence)
SELECT INSTR('Hello World', 'o')       FROM DUAL;  -- 5 (first 'o')
SELECT INSTR('Hello World', 'o', 1, 2) FROM DUAL;  -- 8 (second 'o')

-- REPLACE
SELECT REPLACE('Hello World', 'World', 'Oracle') FROM DUAL;  -- 'Hello Oracle'

-- TRIM, LTRIM, RTRIM
SELECT TRIM('   Hello   ')    FROM DUAL;  -- 'Hello'
SELECT LTRIM('   Hello   ')   FROM DUAL;  -- 'Hello   '
SELECT RTRIM('   Hello   ')   FROM DUAL;  -- '   Hello'
SELECT TRIM('x' FROM 'xxxHelloxxx') FROM DUAL;  -- 'Hello'

-- LPAD, RPAD (padding)
SELECT LPAD('42', 5, '0') FROM DUAL;  -- '00042'
SELECT RPAD('Hi', 10, '.') FROM DUAL;  -- 'Hi........'

-- CONCAT (or || operator)
SELECT CONCAT('Hello', ' World') FROM DUAL;     -- 'Hello World'
SELECT 'Hello' || ' ' || 'World' FROM DUAL;     -- 'Hello World' (more flexible)
```

### Date Functions

```sql
-- Current date/time
SELECT SYSDATE FROM DUAL;                  -- Current date + time
SELECT CURRENT_TIMESTAMP FROM DUAL;        -- With timezone
SELECT SYSTIMESTAMP FROM DUAL;             -- High precision

-- Date arithmetic (Oracle stores dates as numbers internally)
SELECT SYSDATE + 7 FROM DUAL;             -- 7 days from now
SELECT SYSDATE - 30 FROM DUAL;            -- 30 days ago
SELECT hire_date + 90 FROM employees;      -- 90 days after hire

-- Difference between dates (returns number of days)
SELECT SYSDATE - hire_date AS days_employed FROM employees;

-- MONTHS_BETWEEN
SELECT MONTHS_BETWEEN(SYSDATE, hire_date) FROM employees;

-- ADD_MONTHS
SELECT ADD_MONTHS(SYSDATE, 6) FROM DUAL;  -- 6 months from now

-- NEXT_DAY
SELECT NEXT_DAY(SYSDATE, 'FRIDAY') FROM DUAL;  -- Next Friday

-- LAST_DAY
SELECT LAST_DAY(SYSDATE) FROM DUAL;       -- Last day of current month

-- ROUND and TRUNC for dates
SELECT ROUND(SYSDATE, 'MONTH') FROM DUAL;  -- Rounded to nearest month
SELECT TRUNC(SYSDATE, 'YEAR') FROM DUAL;   -- Jan 1 of current year

-- EXTRACT
SELECT EXTRACT(YEAR FROM SYSDATE) FROM DUAL;    -- 2026
SELECT EXTRACT(MONTH FROM SYSDATE) FROM DUAL;   -- 2
SELECT EXTRACT(DAY FROM SYSDATE) FROM DUAL;     -- 11
```

### Numeric Functions

```sql
SELECT ROUND(45.678, 2)  FROM DUAL;  -- 45.68
SELECT ROUND(45.678, 0)  FROM DUAL;  -- 46
SELECT ROUND(45.678, -1) FROM DUAL;  -- 50 (round to nearest 10)

SELECT TRUNC(45.678, 2)  FROM DUAL;  -- 45.67 (no rounding, just cut)
SELECT TRUNC(45.678, 0)  FROM DUAL;  -- 45

SELECT CEIL(45.1)   FROM DUAL;  -- 46 (round up)
SELECT FLOOR(45.9)  FROM DUAL;  -- 45 (round down)

SELECT MOD(10, 3)   FROM DUAL;  -- 1 (remainder: 10 / 3 = 3 remainder 1)
SELECT ABS(-42)     FROM DUAL;  -- 42 (absolute value)
SELECT POWER(2, 10) FROM DUAL;  -- 1024 (2^10)
SELECT SQRT(144)    FROM DUAL;  -- 12
```

### Conversion Functions

```sql
-- TO_CHAR — convert to string
SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD')          FROM DUAL;  -- '2026-02-11'
SELECT TO_CHAR(SYSDATE, 'DD-Mon-YYYY HH24:MI') FROM DUAL;  -- '11-Feb-2026 14:30'
SELECT TO_CHAR(salary, '$999,999.99')           FROM employees;  -- '$75,000.00'

-- TO_DATE — convert string to date
SELECT TO_DATE('2024-03-15', 'YYYY-MM-DD') FROM DUAL;
SELECT TO_DATE('15/03/2024', 'DD/MM/YYYY') FROM DUAL;

-- TO_NUMBER — convert string to number
SELECT TO_NUMBER('12345.67') FROM DUAL;
SELECT TO_NUMBER('$1,234.56', '$9,999.99') FROM DUAL;

-- NVL — replace NULL with a default value
SELECT NVL(commission_pct, 0) FROM employees;  -- NULL becomes 0

-- NVL2(expr, value_if_not_null, value_if_null)
SELECT NVL2(commission_pct, 'Has Commission', 'No Commission') FROM employees;

-- NULLIF — returns NULL if two values are equal
SELECT NULLIF(salary, 0) FROM employees;  -- Returns NULL if salary is 0

-- COALESCE — returns first non-NULL value
SELECT COALESCE(commission_pct, bonus_pct, 0) FROM employees;
-- Tries commission_pct first, then bonus_pct, then 0

-- CASE expression
SELECT first_name, salary,
    CASE 
        WHEN salary > 80000 THEN 'High'
        WHEN salary > 50000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_grade
FROM employees;

-- DECODE (Oracle-specific, like a simple CASE)
SELECT first_name, dept_id,
    DECODE(dept_id, 10, 'Finance', 20, 'IT', 30, 'Sales', 'Other') AS dept_name
FROM employees;
```

---

## 12. GROUP BY & HAVING

### GROUP BY

```sql
-- Group rows that have the same values and apply aggregate functions
SELECT dept_id, COUNT(*) AS emp_count, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id;

-- Result:
-- dept_id | emp_count | avg_salary
-- --------+-----------+-----------
--      10 |         3 |   55000.00
--      20 |         5 |   70000.00
--      30 |         4 |   48000.00
```

**Rule:** Every column in SELECT must either be in GROUP BY or inside an aggregate function!

```sql
-- ❌ WRONG:
SELECT dept_id, first_name, AVG(salary) FROM employees GROUP BY dept_id;
-- first_name is not in GROUP BY and not aggregated!

-- ✅ CORRECT:
SELECT dept_id, AVG(salary) FROM employees GROUP BY dept_id;
```

### GROUP BY with Multiple Columns

```sql
SELECT dept_id, job_id, COUNT(*), AVG(salary)
FROM employees
GROUP BY dept_id, job_id
ORDER BY dept_id;
-- Groups by unique (dept_id, job_id) combinations
```

### HAVING — Filter Groups

```sql
-- WHERE filters ROWS (before grouping)
-- HAVING filters GROUPS (after grouping)

-- Show departments with average salary > 60000
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 60000;

-- Combine WHERE and HAVING
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
WHERE hire_date > TO_DATE('2020-01-01', 'YYYY-MM-DD')  -- filter rows first
GROUP BY dept_id                                          -- then group
HAVING AVG(salary) > 60000                                -- then filter groups
ORDER BY avg_sal DESC;                                    -- then sort
```

---

## 13. Joins

Joins combine rows from two or more tables based on related columns.

### INNER JOIN

```sql
-- Returns only rows where there's a match in BOTH tables
SELECT e.first_name, e.salary, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;

-- Visual:
-- employees       departments
-- ┌───┬────┐      ┌───┬───────┐
-- │ 1 │ 10 │      │10 │ Sales │
-- │ 2 │ 20 │  ──► │20 │ IT    │     Only matching dept_ids appear
-- │ 3 │ 30 │      │30 │ HR    │
-- │ 4 │NULL│      │40 │ Legal │     emp 4 (NULL dept) excluded
-- └───┴────┘      └───┴───────┘     dept 40 (no employees) excluded
```

### LEFT JOIN (LEFT OUTER JOIN)

```sql
-- Returns ALL rows from the LEFT table + matching rows from the RIGHT table
-- If no match, NULLs for right table columns
SELECT e.first_name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- emp 4 (NULL dept) IS included → dept_name will be NULL
-- dept 40 (no employees) is excluded
```

### RIGHT JOIN (RIGHT OUTER JOIN)

```sql
-- Returns ALL rows from the RIGHT table + matching rows from the LEFT table
SELECT e.first_name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- dept 40 IS included → first_name will be NULL
-- emp 4 (NULL dept) is excluded
```

### FULL OUTER JOIN

```sql
-- Returns ALL rows from BOTH tables
-- NULLs where there's no match on either side
SELECT e.first_name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;

-- Both emp 4 AND dept 40 are included
```

### CROSS JOIN

```sql
-- Every row from table A combined with every row from table B
-- Also called "Cartesian Product"
-- If A has 10 rows and B has 5, result has 50 rows!
SELECT e.first_name, d.dept_name
FROM employees e
CROSS JOIN departments d;

-- Use case: Generate all possible combinations
-- (e.g., all product-color combinations)
```

### Self Join

```sql
-- Join a table to itself! Used for hierarchical data.
-- Example: Find each employee's manager name

SELECT e.first_name AS employee, m.first_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;

-- employee │ manager
-- ─────────┼─────────
-- Alice    │ Bob       ← Alice's manager_id points to Bob's emp_id
-- Bob      │ NULL      ← Bob has no manager (CEO)
```

### Multiple Joins

```sql
-- Join 3 or more tables
SELECT e.first_name, d.dept_name, l.city
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id
INNER JOIN locations l ON d.location_id = l.location_id
WHERE l.country_id = 'US';
```

### Join Performance — Under the Hood

Oracle uses three join methods internally:

1. **Nested Loop Join**: For small datasets. Loops through table A, for each row finds matching rows in table B using an index.
2. **Hash Join**: For large datasets without indexes. Builds a hash table from the smaller table, then probes it with the larger table.
3. **Sort-Merge Join**: For pre-sorted data. Sorts both tables, then merges them.

The **optimizer** chooses the best method based on table sizes, indexes, and statistics.

---

## 14. Subqueries

A subquery is a query inside another query.

### Single-Row Subquery

```sql
-- Returns exactly ONE value
-- Find employees who earn more than the average salary
SELECT first_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Find the employee with the highest salary
SELECT first_name, salary
FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
```

### Multi-Row Subquery

```sql
-- Returns multiple values — use IN, ANY, ALL
-- Find employees in departments located in 'New York'
SELECT first_name
FROM employees
WHERE dept_id IN (
    SELECT dept_id FROM departments WHERE location = 'New York'
);

-- ANY — true if condition matches ANY value in the list
SELECT first_name, salary FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE dept_id = 20);
-- salary > the MINIMUM salary in dept 20

-- ALL — true if condition matches ALL values in the list
SELECT first_name, salary FROM employees
WHERE salary > ALL (SELECT salary FROM employees WHERE dept_id = 20);
-- salary > the MAXIMUM salary in dept 20
```

### Correlated Subquery

```sql
-- References columns from the OUTER query — runs once per outer row!
-- Find employees who earn more than their department's average
SELECT e.first_name, e.salary, e.dept_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id
    --                                                ^^^^^^^^
    --                                   References outer query's dept_id!
);

-- EXISTS — check if subquery returns any rows
SELECT d.dept_name
FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id
);
-- Returns departments that have at least one employee
```

**Performance Note:** Correlated subqueries can be slow because they execute once for each row in the outer query. Often you can rewrite them as JOINs for better performance.

---

## 15. Set Operations

Combine results from multiple SELECT statements.

```sql
-- UNION — combines results, removes duplicates
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- UNION ALL — combines results, keeps duplicates (faster!)
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;

-- INTERSECT — only rows that appear in BOTH queries
SELECT city FROM customers
INTERSECT
SELECT city FROM suppliers;

-- MINUS — rows in first query but NOT in second query
SELECT city FROM customers
MINUS
SELECT city FROM suppliers;
```

**Rules:**
1. Both queries must have the **same number of columns**.
2. Corresponding columns must have **compatible data types**.
3. Column names come from the **first** query.

---

## 16. Views

A view is a **virtual table** — a stored SQL query that acts like a table.

```sql
-- Create a view
CREATE VIEW high_earners AS
SELECT emp_id, first_name, last_name, salary, dept_id
FROM employees
WHERE salary > 80000;

-- Use it like a table
SELECT * FROM high_earners;
SELECT first_name FROM high_earners WHERE dept_id = 10;

-- Create or Replace (update existing view)
CREATE OR REPLACE VIEW high_earners AS
SELECT emp_id, first_name, last_name, salary, dept_id
FROM employees
WHERE salary > 90000;  -- changed threshold

-- Drop a view (doesn't affect underlying data!)
DROP VIEW high_earners;

-- View with read-only restriction
CREATE VIEW dept_summary AS
SELECT dept_id, COUNT(*) AS emp_count, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id
WITH READ ONLY;
```

**Under the Hood:**
- A view does NOT store data. It stores the SQL query.
- When you `SELECT * FROM high_earners`, Oracle internally replaces it with the view's query.
- This is called **view merging** — the optimizer merges the view query with your query.
- Views are great for security (hide sensitive columns) and simplicity (hide complex joins).

**Updatable Views:**
- Simple views (single table, no aggregates, no DISTINCT) can be updated.
- Complex views (joins, aggregates, GROUP BY) are generally read-only.

---

## Quick Reference Cheat Sheet

```
DDL: CREATE, ALTER, DROP, TRUNCATE  → Define structure
DML: INSERT, UPDATE, DELETE         → Manipulate data
DQL: SELECT                         → Query data
DCL: GRANT, REVOKE                  → Control access
TCL: COMMIT, ROLLBACK, SAVEPOINT   → Transaction control

Execution Order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY

NULL Rules: NULL + anything = NULL, NULL = NULL → NULL (not TRUE)

Join Types: INNER (matching only), LEFT/RIGHT (all from one side), FULL (all from both), CROSS (all × all)
```

---

*Next: [02-Oracle-PLSQL.md](02-Oracle-PLSQL.md) — PL/SQL Programming*
