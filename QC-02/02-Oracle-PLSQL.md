# Oracle PL/SQL — From Zero to Expert

## Table of Contents
1. [What Is PL/SQL?](#1-what-is-plsql)
2. [PL/SQL Block Structure](#2-plsql-block-structure)
3. [Variables, %TYPE and %ROWTYPE](#3-variables-type-and-rowtype)
4. [Control Structures](#4-control-structures)
5. [Cursors](#5-cursors)
6. [Stored Procedures](#6-stored-procedures)
7. [Functions](#7-functions)
8. [Packages](#8-packages)
9. [Triggers](#9-triggers)
10. [Exception Handling](#10-exception-handling)
11. [Dynamic SQL — EXECUTE IMMEDIATE](#11-dynamic-sql)
12. [Bulk Processing](#12-bulk-processing)

---

## 1. What Is PL/SQL?

**PL/SQL** = **Procedural Language extensions to SQL**

SQL alone is **declarative** — you say *what* you want, not *how* to get it. But sometimes you need:
- Loops
- Conditions (if/else)
- Variables
- Error handling
- Reusable code (procedures, functions)

That's where PL/SQL comes in. It wraps SQL with procedural programming features.

```
┌───────────────────────────────────────────────┐
│                  PL/SQL Engine                │
│  ┌──────────────────┐  ┌────────────────────┐ │
│  │ PL/SQL Executor  │  │   SQL Executor     │ │
│  │ (Procedural      │  │   (Passes SQL to   │ │
│  │  statements)     │  │    the SQL engine) │ │
│  └────────┬─────────┘  └────────┬───────────┘ │
│           │                     │             │
│           └─────────┬───────────┘             │
│                     │                         │
│              PL/SQL Block                     │
└───────────────────────────────────────────────┘
```

**Under the Hood:**
- When you submit a PL/SQL block, Oracle's PL/SQL engine processes the procedural parts.
- Every time it encounters a SQL statement (SELECT, INSERT, etc.), it sends it to the **SQL engine**.
- This back-and-forth between PL/SQL and SQL engines is called **context switching**.
- Too many context switches = slow performance (that's why bulk processing exists — we'll learn that later).

---

## 2. PL/SQL Block Structure

Every PL/SQL program is made of **blocks**. There are three types:

```
┌──────────────────────────────────────┐
│  Anonymous Block (unnamed, one-time) │
│  Procedure (named, stored, reusable) │
│  Function (named, returns a value)   │
└──────────────────────────────────────┘
```

### Anatomy of a Block

```sql
DECLARE        -- Optional: Declare variables, cursors, constants
    -- variable declarations go here
BEGIN          -- Mandatory: Executable statements
    -- your code goes here (SQL + procedural logic)
EXCEPTION      -- Optional: Error handling
    -- handle errors here
END;           -- Mandatory: End of block
/              -- The slash tells SQL*Plus to execute the block
```

### Hello World in PL/SQL

```sql
-- Anonymous block (no name, just runs once)
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, World!');
END;
/

-- To see output, first enable it:
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, World!');
END;
/
-- Output: Hello, World!
```

### Block with Variables

```sql
DECLARE
    v_name      VARCHAR2(50) := 'Alice';   -- := is the assignment operator
    v_salary    NUMBER(10,2) := 75000.00;
    v_bonus     NUMBER(10,2);
    c_tax_rate  CONSTANT NUMBER := 0.30;   -- CONSTANT = cannot be changed
BEGIN
    v_bonus := v_salary * 0.10;            -- Calculate bonus
    
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
    DBMS_OUTPUT.PUT_LINE('Bonus: ' || v_bonus);
    DBMS_OUTPUT.PUT_LINE('Tax: ' || v_salary * c_tax_rate);
    
    -- c_tax_rate := 0.25;  -- ❌ ERROR! Cannot assign to a constant
END;
/
```

### Nested Blocks

```sql
DECLARE
    v_outer VARCHAR2(20) := 'Outer';
BEGIN
    DBMS_OUTPUT.PUT_LINE(v_outer);  -- ✅ Works
    
    -- Inner block (nested)
    DECLARE
        v_inner VARCHAR2(20) := 'Inner';
    BEGIN
        DBMS_OUTPUT.PUT_LINE(v_outer);  -- ✅ Inner can see outer variables
        DBMS_OUTPUT.PUT_LINE(v_inner);  -- ✅ Works
    END;
    
    -- DBMS_OUTPUT.PUT_LINE(v_inner);  -- ❌ ERROR! Outer cannot see inner variables
END;
/
```

---

## 3. Variables, %TYPE and %ROWTYPE

### Variable Types

```sql
DECLARE
    -- Scalar types (single values)
    v_name      VARCHAR2(100);
    v_age       NUMBER(3);
    v_is_active BOOLEAN := TRUE;   -- Note: BOOLEAN exists in PL/SQL but NOT in SQL!
    v_hire_date DATE := SYSDATE;
    
    -- Anchored types using %TYPE
    v_emp_name  employees.first_name%TYPE;  -- Same type as employees.first_name column
    v_emp_sal   employees.salary%TYPE;
    
    -- Record type using %ROWTYPE
    v_emp_row   employees%ROWTYPE;  -- One variable that holds an ENTIRE row
BEGIN
    -- Using %TYPE variables
    SELECT first_name, salary INTO v_emp_name, v_emp_sal
    FROM employees WHERE emp_id = 100;
    DBMS_OUTPUT.PUT_LINE(v_emp_name || ' earns ' || v_emp_sal);
    
    -- Using %ROWTYPE variable
    SELECT * INTO v_emp_row
    FROM employees WHERE emp_id = 100;
    DBMS_OUTPUT.PUT_LINE(v_emp_row.first_name || ' in dept ' || v_emp_row.dept_id);
END;
/
```

### Why Use %TYPE and %ROWTYPE?

```
Without %TYPE:
  v_salary NUMBER(10,2);
  -- If someone changes employees.salary from NUMBER(10,2) to NUMBER(12,2),
  -- your code might break or truncate data!

With %TYPE:
  v_salary employees.salary%TYPE;
  -- Always matches the column. If the column changes, your variable changes too.
  -- This is called "anchored declaration" — it's anchored to the column definition.
```

### Custom Record Types

```sql
DECLARE
    -- Define your own record type
    TYPE t_employee IS RECORD (
        name      VARCHAR2(100),
        salary    NUMBER(10,2),
        dept_name VARCHAR2(50)
    );
    
    v_emp t_employee;  -- Variable of our custom type
BEGIN
    SELECT e.first_name || ' ' || e.last_name, e.salary, d.dept_name
    INTO v_emp.name, v_emp.salary, v_emp.dept_name
    FROM employees e
    JOIN departments d ON e.dept_id = d.dept_id
    WHERE e.emp_id = 100;
    
    DBMS_OUTPUT.PUT_LINE(v_emp.name || ' | ' || v_emp.salary || ' | ' || v_emp.dept_name);
END;
/
```

### PL/SQL Collections (Associative Arrays, Nested Tables, VARRAYs)

```sql
DECLARE
    -- Associative Array (like a map/dictionary) — index by string or number
    TYPE t_salary_map IS TABLE OF NUMBER INDEX BY VARCHAR2(100);
    v_salaries t_salary_map;
    
    -- Nested Table (ordered list, can grow)
    TYPE t_name_list IS TABLE OF VARCHAR2(100);
    v_names t_name_list := t_name_list();  -- Initialize empty
    
    -- VARRAY (fixed-size array)
    TYPE t_top5 IS VARRAY(5) OF VARCHAR2(100);
    v_top5 t_top5 := t_top5();
BEGIN
    -- Associative Array
    v_salaries('Alice') := 75000;
    v_salaries('Bob')   := 65000;
    DBMS_OUTPUT.PUT_LINE('Alice earns: ' || v_salaries('Alice'));
    
    -- Nested Table
    v_names.EXTEND(3);           -- Make room for 3 elements
    v_names(1) := 'Alice';
    v_names(2) := 'Bob';
    v_names(3) := 'Charlie';
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_names.COUNT);
    
    -- VARRAY
    v_top5.EXTEND(2);
    v_top5(1) := 'First';
    v_top5(2) := 'Second';
END;
/
```

---

## 4. Control Structures

### IF-THEN-ELSE

```sql
DECLARE
    v_salary NUMBER := 75000;
    v_grade  VARCHAR2(10);
BEGIN
    IF v_salary > 100000 THEN
        v_grade := 'A';
    ELSIF v_salary > 70000 THEN     -- Note: ELSIF (not ELSEIF or ELSE IF!)
        v_grade := 'B';
    ELSIF v_salary > 50000 THEN
        v_grade := 'C';
    ELSE
        v_grade := 'D';
    END IF;                         -- Don't forget END IF with a space!
    
    DBMS_OUTPUT.PUT_LINE('Grade: ' || v_grade);
END;
/
```

### CASE Statement

```sql
DECLARE
    v_dept_id   NUMBER := 20;
    v_dept_name VARCHAR2(50);
BEGIN
    -- Simple CASE (exact match)
    CASE v_dept_id
        WHEN 10 THEN v_dept_name := 'Finance';
        WHEN 20 THEN v_dept_name := 'IT';
        WHEN 30 THEN v_dept_name := 'Sales';
        ELSE v_dept_name := 'Unknown';
    END CASE;
    
    DBMS_OUTPUT.PUT_LINE('Department: ' || v_dept_name);
END;
/

-- Searched CASE (conditions)
DECLARE
    v_salary NUMBER := 85000;
BEGIN
    CASE
        WHEN v_salary > 100000 THEN DBMS_OUTPUT.PUT_LINE('Executive');
        WHEN v_salary > 70000  THEN DBMS_OUTPUT.PUT_LINE('Senior');
        WHEN v_salary > 40000  THEN DBMS_OUTPUT.PUT_LINE('Mid-level');
        ELSE DBMS_OUTPUT.PUT_LINE('Junior');
    END CASE;
END;
/
```

### Loops

#### Basic LOOP (like do-while)

```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
        EXIT WHEN v_counter > 5;  -- Exit condition (MUST have one, or infinite loop!)
    END LOOP;
END;
/
-- Output: Counter: 1, 2, 3, 4, 5
```

#### WHILE LOOP

```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    WHILE v_counter <= 5 LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
    END LOOP;
END;
/
```

#### FOR LOOP

```sql
BEGIN
    -- FOR loop automatically handles the loop variable
    -- No need to declare it! It's automatically declared and scoped to the loop.
    FOR i IN 1..10 LOOP
        DBMS_OUTPUT.PUT_LINE('i = ' || i);
    END LOOP;
    -- i doesn't exist here anymore
    
    -- Reverse loop
    FOR i IN REVERSE 1..10 LOOP
        DBMS_OUTPUT.PUT_LINE('i = ' || i);  -- 10, 9, 8, ..., 1
    END LOOP;
END;
/
```

#### CONTINUE and EXIT

```sql
BEGIN
    FOR i IN 1..10 LOOP
        CONTINUE WHEN MOD(i, 2) = 0;  -- Skip even numbers
        DBMS_OUTPUT.PUT_LINE(i);        -- Only prints: 1, 3, 5, 7, 9
    END LOOP;
    
    FOR i IN 1..100 LOOP
        EXIT WHEN i > 5;              -- Exit the loop early
        DBMS_OUTPUT.PUT_LINE(i);       -- Prints: 1, 2, 3, 4, 5
    END LOOP;
END;
/
```

---

## 5. Cursors

A **cursor** is a pointer to a result set returned by a SQL query. It lets you process rows **one at a time**.

### How Cursors Work Under the Hood

```
  Your Query: SELECT * FROM employees WHERE dept_id = 10
       │
       ▼
  ┌──────────────────────────────────────────┐
  │          Private SQL Area (PGA)          │
  │  ┌────────────────────────────────────┐  │
  │  │   Parsed Query + Execution Plan    │  │
  │  ├────────────────────────────────────┤  │
  │  │   Result Set (rows)                │  │
  │  │   ┌──── Row 1 (Alice, 75000) ◄──── │──│── CURSOR points here
  │  │   ├──── Row 2 (Bob, 65000)         │  │
  │  │   └──── Row 3 (Charlie, 52000)     │  │
  │  └────────────────────────────────────┘  │
  └──────────────────────────────────────────┘

  OPEN   → Execute query, create result set
  FETCH  → Move cursor to next row, retrieve data
  CLOSE  → Free memory
```

### Implicit Cursors

Oracle automatically creates an implicit cursor for every SQL statement.

```sql
BEGIN
    UPDATE employees SET salary = salary * 1.10 WHERE dept_id = 10;
    
    -- SQL%ROWCOUNT — number of rows affected
    DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' rows updated');
    
    -- SQL%FOUND — TRUE if the last statement affected at least one row
    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Update successful');
    END IF;
    
    -- SQL%NOTFOUND — TRUE if the last statement affected zero rows
    DELETE FROM employees WHERE emp_id = 9999;
    IF SQL%NOTFOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee with that ID');
    END IF;
    
    COMMIT;
END;
/
```

### Explicit Cursors

You declare, open, fetch, and close them manually.

```sql
DECLARE
    -- Step 1: DECLARE the cursor
    CURSOR c_emp IS
        SELECT emp_id, first_name, salary
        FROM employees
        WHERE dept_id = 10
        ORDER BY salary DESC;
    
    v_id     employees.emp_id%TYPE;
    v_name   employees.first_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    -- Step 2: OPEN the cursor (executes the query)
    OPEN c_emp;
    
    -- Step 3: FETCH rows one at a time
    LOOP
        FETCH c_emp INTO v_id, v_name, v_salary;
        EXIT WHEN c_emp%NOTFOUND;  -- Exit when no more rows
        
        DBMS_OUTPUT.PUT_LINE(v_id || ': ' || v_name || ' - $' || v_salary);
    END LOOP;
    
    DBMS_OUTPUT.PUT_LINE('Total rows: ' || c_emp%ROWCOUNT);
    
    -- Step 4: CLOSE the cursor (free memory)
    CLOSE c_emp;
END;
/
```

### Cursor FOR Loop (Easiest Way!)

```sql
-- Oracle automatically:
-- 1. Opens the cursor
-- 2. Fetches each row into a record
-- 3. Closes the cursor when done
-- No need to declare variables, OPEN, FETCH, or CLOSE!

BEGIN
    FOR rec IN (SELECT emp_id, first_name, salary FROM employees WHERE dept_id = 10) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.first_name || ' earns ' || rec.salary);
    END LOOP;
    -- Cursor is automatically closed here
END;
/

-- Or with a named cursor:
DECLARE
    CURSOR c_emp IS
        SELECT emp_id, first_name, salary FROM employees WHERE dept_id = 10;
BEGIN
    FOR rec IN c_emp LOOP
        DBMS_OUTPUT.PUT_LINE(rec.first_name || ' earns ' || rec.salary);
    END LOOP;
END;
/
```

### Parameterized Cursors

```sql
DECLARE
    -- Cursor that accepts a parameter
    CURSOR c_emp(p_dept_id NUMBER, p_min_salary NUMBER DEFAULT 0) IS
        SELECT first_name, salary
        FROM employees
        WHERE dept_id = p_dept_id AND salary > p_min_salary;
BEGIN
    DBMS_OUTPUT.PUT_LINE('--- Department 10, salary > 50000 ---');
    FOR rec IN c_emp(10, 50000) LOOP
        DBMS_OUTPUT.PUT_LINE(rec.first_name || ': ' || rec.salary);
    END LOOP;
    
    DBMS_OUTPUT.PUT_LINE('--- Department 20, all salaries ---');
    FOR rec IN c_emp(20) LOOP  -- Uses default 0 for min_salary
        DBMS_OUTPUT.PUT_LINE(rec.first_name || ': ' || rec.salary);
    END LOOP;
END;
/
```

### REF Cursors (Dynamic Cursors)

```sql
-- REF CURSOR can point to different queries at runtime
DECLARE
    TYPE t_ref_cursor IS REF CURSOR;  -- Weak ref cursor (any query)
    v_cursor t_ref_cursor;
    v_name   VARCHAR2(50);
    v_salary NUMBER;
BEGIN
    -- Open with one query
    OPEN v_cursor FOR
        SELECT first_name, salary FROM employees WHERE dept_id = 10;
    
    LOOP
        FETCH v_cursor INTO v_name, v_salary;
        EXIT WHEN v_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name || ': ' || v_salary);
    END LOOP;
    CLOSE v_cursor;
    
    -- Reuse with a different query!
    OPEN v_cursor FOR
        SELECT dept_name, 0 FROM departments;
    
    LOOP
        FETCH v_cursor INTO v_name, v_salary;
        EXIT WHEN v_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Dept: ' || v_name);
    END LOOP;
    CLOSE v_cursor;
END;
/

-- SYS_REFCURSOR — Oracle's built-in weak ref cursor type
-- Very common for returning result sets from procedures
DECLARE
    v_cursor SYS_REFCURSOR;
    v_name   VARCHAR2(50);
BEGIN
    OPEN v_cursor FOR SELECT first_name FROM employees WHERE ROWNUM <= 5;
    LOOP
        FETCH v_cursor INTO v_name;
        EXIT WHEN v_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name);
    END LOOP;
    CLOSE v_cursor;
END;
/
```

### Cursor Attributes Summary

| Attribute | Description |
|---|---|
| `%FOUND` | TRUE if the last FETCH returned a row |
| `%NOTFOUND` | TRUE if the last FETCH returned NO row |
| `%ROWCOUNT` | Number of rows fetched so far |
| `%ISOPEN` | TRUE if the cursor is currently open |

---

## 6. Stored Procedures

A **procedure** is a named PL/SQL block stored in the database. It can accept parameters and be called repeatedly.

### Creating a Procedure

```sql
CREATE OR REPLACE PROCEDURE give_raise (
    p_emp_id  IN  NUMBER,                     -- IN = input (read-only, default)
    p_percent IN  NUMBER DEFAULT 10,          -- Default value
    p_new_sal OUT NUMBER                      -- OUT = output (write-only)
) AS
    -- Declaration section (no DECLARE keyword needed!)
    v_current_salary NUMBER;
BEGIN
    -- Get current salary
    SELECT salary INTO v_current_salary
    FROM employees
    WHERE emp_id = p_emp_id;
    
    -- Calculate and apply raise
    p_new_sal := v_current_salary * (1 + p_percent / 100);
    
    UPDATE employees
    SET salary = p_new_sal
    WHERE emp_id = p_emp_id;
    
    COMMIT;
    
    DBMS_OUTPUT.PUT_LINE('Raise applied. New salary: ' || p_new_sal);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee ' || p_emp_id || ' not found!');
        p_new_sal := -1;
END give_raise;
/
```

### Calling a Procedure

```sql
-- From another PL/SQL block:
DECLARE
    v_result NUMBER;
BEGIN
    give_raise(100, 15, v_result);   -- positional parameters
    DBMS_OUTPUT.PUT_LINE('New salary: ' || v_result);
    
    give_raise(p_emp_id => 101, p_percent => 20, p_new_sal => v_result);  -- named parameters
    
    give_raise(101, p_new_sal => v_result);  -- mixed: positional first, then named
END;
/

-- From SQL*Plus:
EXECUTE give_raise(100, 10, :result);
```

### IN, OUT, IN OUT Parameters

```sql
CREATE OR REPLACE PROCEDURE demo_params (
    p_in     IN     NUMBER,      -- Read-only. Cannot assign to it.
    p_out    OUT    NUMBER,      -- Write-only. Starts as NULL inside procedure.
    p_inout  IN OUT NUMBER       -- Read AND write. Pass a value in, modify it, get it back.
) AS
BEGIN
    DBMS_OUTPUT.PUT_LINE('p_in: ' || p_in);       -- Can read
    -- p_in := 100;                                -- ❌ ERROR! Can't modify IN parameter
    
    -- DBMS_OUTPUT.PUT_LINE(p_out);                -- Would print NULL (even if caller passed a value)
    p_out := p_in * 2;                             -- ✅ Can assign
    
    DBMS_OUTPUT.PUT_LINE('p_inout before: ' || p_inout);  -- Can read
    p_inout := p_inout + 100;                              -- Can modify
END;
/

DECLARE
    v_out   NUMBER;
    v_inout NUMBER := 50;
BEGIN
    demo_params(10, v_out, v_inout);
    DBMS_OUTPUT.PUT_LINE('v_out: ' || v_out);       -- 20
    DBMS_OUTPUT.PUT_LINE('v_inout: ' || v_inout);   -- 150
END;
/
```

---

## 7. Functions

A **function** is like a procedure but it **returns a value**. It can be used in SQL statements.

```sql
CREATE OR REPLACE FUNCTION calculate_tax (
    p_salary IN NUMBER,
    p_rate   IN NUMBER DEFAULT 0.30
) RETURN NUMBER                       -- Specify the return type
AS
    v_tax NUMBER;
BEGIN
    v_tax := p_salary * p_rate;
    RETURN v_tax;                     -- MUST return a value
END calculate_tax;
/

-- Use in PL/SQL
DECLARE
    v_tax NUMBER;
BEGIN
    v_tax := calculate_tax(75000);
    DBMS_OUTPUT.PUT_LINE('Tax: ' || v_tax);           -- Tax: 22500
    DBMS_OUTPUT.PUT_LINE('Tax: ' || calculate_tax(75000, 0.25));  -- Tax: 18750
END;
/

-- Use in SQL (functions only, not procedures!)
SELECT first_name, salary, calculate_tax(salary) AS tax
FROM employees;
```

### Procedure vs Function

| Feature | Procedure | Function |
|---|---|---|
| Returns a value? | No (uses OUT params) | Yes (RETURN statement) |
| Used in SQL? | ❌ No | ✅ Yes (if no side effects) |
| Purpose | Perform an action | Compute and return a value |
| RETURN statement | Optional (just exits) | Mandatory |

---

## 8. Packages

A **package** is a container that groups related procedures, functions, types, variables, and cursors together.

```
┌──────────────────────────────────────────────────────┐
│                    PACKAGE                           │
│  ┌─────────────────────────────────────────────────┐ │
│  │  SPECIFICATION (public interface / "header")    │ │
│  │  - Declares what the outside world can see      │ │
│  │  - Public procedures, functions, types, vars    │ │
│  └─────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────┐ │
│  │  BODY (private implementation)                  │ │
│  │  - Contains the actual code                     │ │
│  │  - Can have private procedures/functions too    │ │
│  └─────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

### Package Specification (Header)

```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    -- Public constant
    c_max_salary CONSTANT NUMBER := 200000;
    
    -- Public type
    TYPE t_emp_rec IS RECORD (
        name   VARCHAR2(100),
        salary NUMBER
    );
    
    -- Public procedure declarations
    PROCEDURE hire_employee (
        p_first_name IN VARCHAR2,
        p_last_name  IN VARCHAR2,
        p_salary     IN NUMBER,
        p_dept_id    IN NUMBER
    );
    
    PROCEDURE fire_employee (p_emp_id IN NUMBER);
    
    -- Public function declaration
    FUNCTION get_annual_salary (p_emp_id IN NUMBER) RETURN NUMBER;
    
END emp_pkg;
/
```

### Package Body (Implementation)

```sql
CREATE OR REPLACE PACKAGE BODY emp_pkg AS
    -- Private variable (only visible inside the package body)
    v_employee_count NUMBER := 0;
    
    -- Private procedure (not in spec, so outsiders can't call it)
    PROCEDURE log_action (p_message VARCHAR2) IS
    BEGIN
        INSERT INTO audit_log (log_date, message)
        VALUES (SYSDATE, p_message);
    END log_action;
    
    -- Public procedure implementation
    PROCEDURE hire_employee (
        p_first_name IN VARCHAR2,
        p_last_name  IN VARCHAR2,
        p_salary     IN NUMBER,
        p_dept_id    IN NUMBER
    ) IS
    BEGIN
        IF p_salary > c_max_salary THEN
            RAISE_APPLICATION_ERROR(-20001, 'Salary exceeds maximum!');
        END IF;
        
        INSERT INTO employees (emp_id, first_name, last_name, salary, dept_id, hire_date)
        VALUES (emp_seq.NEXTVAL, p_first_name, p_last_name, p_salary, p_dept_id, SYSDATE);
        
        v_employee_count := v_employee_count + 1;
        log_action('Hired: ' || p_first_name || ' ' || p_last_name);
        COMMIT;
    END hire_employee;
    
    PROCEDURE fire_employee (p_emp_id IN NUMBER) IS
    BEGIN
        DELETE FROM employees WHERE emp_id = p_emp_id;
        IF SQL%ROWCOUNT = 0 THEN
            RAISE_APPLICATION_ERROR(-20002, 'Employee not found!');
        END IF;
        log_action('Fired employee ID: ' || p_emp_id);
        COMMIT;
    END fire_employee;
    
    FUNCTION get_annual_salary (p_emp_id IN NUMBER) RETURN NUMBER IS
        v_salary NUMBER;
    BEGIN
        SELECT salary * 12 INTO v_salary FROM employees WHERE emp_id = p_emp_id;
        RETURN v_salary;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RETURN -1;
    END get_annual_salary;

END emp_pkg;
/
```

### Using a Package

```sql
-- Call package members with dot notation
BEGIN
    emp_pkg.hire_employee('John', 'Doe', 60000, 10);
    
    DBMS_OUTPUT.PUT_LINE('Annual: ' || emp_pkg.get_annual_salary(100));
    
    DBMS_OUTPUT.PUT_LINE('Max salary: ' || emp_pkg.c_max_salary);
END;
/
```

### Why Packages?

1. **Encapsulation** — Hide implementation details, expose clean interface.
2. **Performance** — When you call one package member, the whole package is loaded into memory. Subsequent calls are faster.
3. **Organization** — Group related code together.
4. **State** — Package variables persist for the session (like global variables).
5. **Overloading** — Can have multiple procedures/functions with the same name but different parameters.

---

## 9. Triggers

A **trigger** is a PL/SQL block that automatically fires when a specific event happens on a table.

```
Event → Trigger Fires → Your Code Runs
```

### Trigger Timing & Events

```
BEFORE / AFTER / INSTEAD OF
  ×
INSERT / UPDATE / DELETE
  ×
Row-level (FOR EACH ROW) / Statement-level

Example: BEFORE INSERT, FOR EACH ROW → fires before each row is inserted
```

### Basic Trigger

```sql
-- Audit trigger: log every salary change
CREATE OR REPLACE TRIGGER trg_salary_audit
BEFORE UPDATE OF salary ON employees    -- Fires before salary is updated
FOR EACH ROW                            -- Fires for EACH row being updated
WHEN (OLD.salary != NEW.salary)         -- Only when salary actually changes
BEGIN
    INSERT INTO salary_audit (
        emp_id, old_salary, new_salary, changed_by, changed_date
    ) VALUES (
        :OLD.emp_id,                    -- :OLD = value BEFORE the update
        :OLD.salary,
        :NEW.salary,                    -- :NEW = value AFTER the update
        USER,
        SYSDATE
    );
END;
/

-- Now, whenever someone runs:
UPDATE employees SET salary = 80000 WHERE emp_id = 100;
-- The trigger automatically inserts a row into salary_audit!
```

### :OLD and :NEW

| Trigger Event | :OLD | :NEW |
|---|---|---|
| INSERT | NULL (no old data) | The new row being inserted |
| UPDATE | The row before update | The row after update |
| DELETE | The row being deleted | NULL (no new data) |

### Statement-Level Trigger (No FOR EACH ROW)

```sql
-- Fires once per SQL statement, regardless of how many rows affected
CREATE OR REPLACE TRIGGER trg_emp_dml
AFTER INSERT OR UPDATE OR DELETE ON employees
BEGIN
    IF INSERTING THEN
        DBMS_OUTPUT.PUT_LINE('Insert happened on employees');
    ELSIF UPDATING THEN
        DBMS_OUTPUT.PUT_LINE('Update happened on employees');
    ELSIF DELETING THEN
        DBMS_OUTPUT.PUT_LINE('Delete happened on employees');
    END IF;
END;
/
```

### INSTEAD OF Trigger (For Views)

```sql
-- Views with joins are not directly updatable
-- INSTEAD OF triggers let you define what happens when someone tries to update a view
CREATE OR REPLACE TRIGGER trg_emp_view_insert
INSTEAD OF INSERT ON emp_dept_view
FOR EACH ROW
BEGIN
    INSERT INTO employees (emp_id, first_name, dept_id)
    VALUES (emp_seq.NEXTVAL, :NEW.first_name, :NEW.dept_id);
END;
/
```

---

## 10. Exception Handling

### Exception Hierarchy

```
                    EXCEPTION
                    /        \
           PREDEFINED      USER-DEFINED
           /    |    \
    NO_DATA_FOUND  TOO_MANY_ROWS  ZERO_DIVIDE  ...
```

### Predefined Exceptions

```sql
BEGIN
    DECLARE
        v_name VARCHAR2(50);
        v_result NUMBER;
    BEGIN
        -- NO_DATA_FOUND: SELECT INTO returns 0 rows
        SELECT first_name INTO v_name FROM employees WHERE emp_id = 99999;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('Employee not found!');
        WHEN TOO_MANY_ROWS THEN
            DBMS_OUTPUT.PUT_LINE('Query returned multiple rows!');
        WHEN ZERO_DIVIDE THEN
            DBMS_OUTPUT.PUT_LINE('Cannot divide by zero!');
        WHEN OTHERS THEN     -- Catch-all (like catch(Exception e) in Java)
            DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);     -- Error message
            DBMS_OUTPUT.PUT_LINE('Code: ' || SQLCODE);       -- Error number
    END;
END;
/
```

### Common Predefined Exceptions

| Exception | ORA Code | When It Happens |
|---|---|---|
| `NO_DATA_FOUND` | ORA-01403 | SELECT INTO returns 0 rows |
| `TOO_MANY_ROWS` | ORA-01422 | SELECT INTO returns > 1 row |
| `ZERO_DIVIDE` | ORA-01476 | Division by zero |
| `VALUE_ERROR` | ORA-06502 | Arithmetic, conversion, truncation error |
| `INVALID_CURSOR` | ORA-01001 | Operating on a cursor that isn't open |
| `DUP_VAL_ON_INDEX` | ORA-00001 | Unique constraint violated |
| `CURSOR_ALREADY_OPEN` | ORA-06511 | Opening a cursor that's already open |
| `LOGIN_DENIED` | ORA-01017 | Invalid username/password |

### User-Defined Exceptions

```sql
DECLARE
    -- Declare a custom exception
    e_salary_too_high EXCEPTION;
    e_negative_salary EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_negative_salary, -20001);  -- Associate with an error code
    
    v_salary NUMBER := 250000;
BEGIN
    IF v_salary > 200000 THEN
        RAISE e_salary_too_high;     -- Raise our custom exception
    END IF;
    
    IF v_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative!');
        -- -20000 to -20999 are reserved for user-defined errors
    END IF;
    
EXCEPTION
    WHEN e_salary_too_high THEN
        DBMS_OUTPUT.PUT_LINE('Error: Salary exceeds maximum allowed!');
    WHEN e_negative_salary THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
```

### Exception Propagation

```sql
-- Exceptions "bubble up" through nested blocks
BEGIN
    BEGIN
        BEGIN
            -- Exception occurs here
            RAISE NO_DATA_FOUND;
        -- No EXCEPTION handler here → propagates up
        END;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('Caught in middle block!');
            -- If you RAISE again here, it propagates to the outer block
    END;
END;
/
```

### Error Logging Pattern

```sql
CREATE OR REPLACE PROCEDURE process_data AS
BEGIN
    -- ... business logic ...
    NULL;
EXCEPTION
    WHEN OTHERS THEN
        -- Log the error
        INSERT INTO error_log (
            error_date, error_code, error_message, error_backtrace
        ) VALUES (
            SYSDATE,
            SQLCODE,
            SQLERRM,
            DBMS_UTILITY.FORMAT_ERROR_BACKTRACE  -- Full stack trace!
        );
        COMMIT;  -- Commit the log entry even if main transaction rolls back
        RAISE;   -- Re-raise the exception so the caller knows something went wrong
END;
/
```

---

## 11. Dynamic SQL — EXECUTE IMMEDIATE

Sometimes you don't know the SQL at compile time. Dynamic SQL lets you build and execute SQL strings at runtime.

```sql
DECLARE
    v_table_name VARCHAR2(30) := 'EMPLOYEES';
    v_sql        VARCHAR2(500);
    v_count      NUMBER;
    v_name       VARCHAR2(50);
BEGIN
    -- Simple dynamic SQL
    v_sql := 'SELECT COUNT(*) FROM ' || v_table_name;
    EXECUTE IMMEDIATE v_sql INTO v_count;
    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
    
    -- With bind variables (ALWAYS use these to prevent SQL injection!)
    v_sql := 'SELECT first_name FROM employees WHERE emp_id = :id';
    EXECUTE IMMEDIATE v_sql INTO v_name USING 100;
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);
    
    -- Dynamic DDL (can't use bind variables for table/column names)
    EXECUTE IMMEDIATE 'CREATE TABLE temp_table (id NUMBER, name VARCHAR2(50))';
    
    -- Dynamic DML with RETURNING
    DECLARE
        v_new_sal NUMBER;
    BEGIN
        EXECUTE IMMEDIATE 
            'UPDATE employees SET salary = salary * 1.10 WHERE emp_id = :1 RETURNING salary INTO :2'
            USING 100
            RETURNING INTO v_new_sal;
        DBMS_OUTPUT.PUT_LINE('New salary: ' || v_new_sal);
    END;
END;
/
```

**⚠️ SQL Injection Warning:**

```sql
-- ❌ DANGEROUS (concatenation — SQL injection possible!)
v_sql := 'SELECT * FROM employees WHERE name = ''' || v_user_input || '''';
-- If v_user_input = "'; DROP TABLE employees; --", disaster!

-- ✅ SAFE (bind variables — Oracle handles escaping)
v_sql := 'SELECT * FROM employees WHERE name = :name';
EXECUTE IMMEDIATE v_sql USING v_user_input;
```

---

## 12. Bulk Processing

### The Context Switch Problem

```
Without Bulk Processing:
  PL/SQL Engine → SQL Engine → PL/SQL → SQL → PL/SQL → SQL → ...
                  (1 row)                (1 row)        (1 row)
  Each switch has overhead. 1 million rows = 1 million switches = SLOW!

With Bulk Processing:
  PL/SQL Engine → SQL Engine → PL/SQL Engine
                  (1000 rows)   (done!)
  Dramatically fewer switches = FAST!
```

### BULK COLLECT — Fetch Many Rows at Once

```sql
DECLARE
    TYPE t_name_tab IS TABLE OF employees.first_name%TYPE;
    TYPE t_sal_tab  IS TABLE OF employees.salary%TYPE;
    
    v_names    t_name_tab;
    v_salaries t_sal_tab;
BEGIN
    -- Fetch ALL rows at once into collections
    SELECT first_name, salary
    BULK COLLECT INTO v_names, v_salaries
    FROM employees
    WHERE dept_id = 10;
    
    -- Process the data (already in memory, no more SQL calls!)
    FOR i IN 1..v_names.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_names(i) || ': ' || v_salaries(i));
    END LOOP;
END;
/

-- With LIMIT (for very large tables — don't load millions of rows into memory!)
DECLARE
    CURSOR c_emp IS SELECT first_name, salary FROM employees;
    TYPE t_name_tab IS TABLE OF employees.first_name%TYPE;
    TYPE t_sal_tab  IS TABLE OF employees.salary%TYPE;
    v_names    t_name_tab;
    v_salaries t_sal_tab;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp BULK COLLECT INTO v_names, v_salaries LIMIT 1000;
        -- Process 1000 rows at a time
        FOR i IN 1..v_names.COUNT LOOP
            -- ... process each row ...
            NULL;
        END LOOP;
        EXIT WHEN v_names.COUNT < 1000;  -- Less than 1000 = last batch
    END LOOP;
    CLOSE c_emp;
END;
/
```

### FORALL — Execute DML for Many Rows at Once

```sql
DECLARE
    TYPE t_id_tab IS TABLE OF NUMBER;
    v_ids t_id_tab := t_id_tab(101, 102, 103, 104, 105);
BEGIN
    -- Instead of looping with individual UPDATEs:
    FORALL i IN 1..v_ids.COUNT
        UPDATE employees 
        SET salary = salary * 1.10 
        WHERE emp_id = v_ids(i);
    
    DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' rows updated');
    COMMIT;
END;
/

-- FORALL with SAVE EXCEPTIONS (continue even if some rows fail)
DECLARE
    TYPE t_id_tab IS TABLE OF NUMBER;
    v_ids t_id_tab := t_id_tab(101, 102, 999, 104);  -- 999 doesn't exist
    e_bulk_errors EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_bulk_errors, -24381);
BEGIN
    FORALL i IN 1..v_ids.COUNT SAVE EXCEPTIONS
        UPDATE employees SET salary = salary * 1.10 WHERE emp_id = v_ids(i);
    COMMIT;
EXCEPTION
    WHEN e_bulk_errors THEN
        FOR j IN 1..SQL%BULK_EXCEPTIONS.COUNT LOOP
            DBMS_OUTPUT.PUT_LINE('Error at index: ' || SQL%BULK_EXCEPTIONS(j).ERROR_INDEX);
            DBMS_OUTPUT.PUT_LINE('Error code: ' || SQL%BULK_EXCEPTIONS(j).ERROR_CODE);
        END LOOP;
END;
/
```

### Performance Comparison

```
Processing 100,000 rows:

Method                    Time
─────────────────────────────────
Row-by-row (cursor loop)  15.2 seconds
BULK COLLECT + FORALL       0.8 seconds   ← ~19x faster!

The difference grows with more rows. For millions of rows,
bulk processing can be 50-100x faster.
```

---

## Quick Reference

```
Block:     DECLARE ... BEGIN ... EXCEPTION ... END;
Variable:  v_name TYPE := value;
Anchored:  v_x table.column%TYPE;  |  v_row table%ROWTYPE;
IF:        IF cond THEN ... ELSIF cond THEN ... ELSE ... END IF;
CASE:      CASE var WHEN val THEN ... END CASE;
FOR:       FOR i IN 1..10 LOOP ... END LOOP;
WHILE:     WHILE cond LOOP ... END LOOP;
CURSOR:    FOR rec IN (SELECT ...) LOOP ... END LOOP;
EXCEPTION: WHEN exc_name THEN ... WHEN OTHERS THEN ...
PROCEDURE: CREATE OR REPLACE PROCEDURE name (params) AS BEGIN ... END;
FUNCTION:  CREATE OR REPLACE FUNCTION name (params) RETURN type AS BEGIN ... RETURN val; END;
PACKAGE:   Specification (public API) + Body (implementation)
TRIGGER:   BEFORE/AFTER INSERT/UPDATE/DELETE ON table FOR EACH ROW
DYNAMIC:   EXECUTE IMMEDIATE 'SQL' INTO var USING bind_var;
BULK:      BULK COLLECT INTO collection  |  FORALL i IN 1..n DML_statement;
```

---

*Previous: [01-Oracle-SQL-Fundamentals.md](01-Oracle-SQL-Fundamentals.md)*
*Next: [03-Java-Basics.md](03-Java-Basics.md)*
