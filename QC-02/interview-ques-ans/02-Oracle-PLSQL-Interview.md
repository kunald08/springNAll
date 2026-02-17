# Oracle PL/SQL — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview.

---

## 1. What is PL/SQL? Why use it over plain SQL?

**Answer:**
PL/SQL stands for **Procedural Language extension to SQL**. While SQL is declarative (you say WHAT you want), PL/SQL adds procedural capabilities — variables, loops, conditions, exception handling — so you can write complex business logic directly in the database.

The main advantage is **reduced context switching**. When you send individual SQL statements from Java to Oracle, each one goes through a network round trip. With PL/SQL, you send a block of code that executes entirely inside the database engine, which is much faster for data-intensive operations.

---

## 2. What is the structure of a PL/SQL block?

**Answer:**
A PL/SQL block has three sections:

```sql
DECLARE     -- Optional: declare variables, cursors, types
    v_name VARCHAR2(50);
BEGIN       -- Required: executable statements
    SELECT first_name INTO v_name FROM employees WHERE id = 1;
    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION   -- Optional: error handling
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Not found');
END;
```

There are **anonymous blocks** (not stored, run once), **stored procedures** (named, stored in DB, no return value), and **functions** (named, stored, MUST return a value).

---

## 3. What is the difference between %TYPE and %ROWTYPE?

**Answer:**
- **%TYPE** anchors a variable to a **single column's data type**. If the column type changes, my variable automatically adapts. `v_name employees.first_name%TYPE;`
- **%ROWTYPE** anchors a variable to an **entire row's structure**. It creates a record with one field for each column. `v_emp employees%ROWTYPE;`

This makes code maintainable — if the table structure changes, I don't have to update my variable declarations.

---

## 4. What is a Cursor? What types are there?

**Answer:**
A cursor is a **pointer to a result set** returned by a query. Oracle processes SQL results row by row through cursors.

**Implicit cursors** are created automatically by Oracle for every DML statement. I can check `SQL%ROWCOUNT`, `SQL%FOUND`, `SQL%NOTFOUND` after any DML.

**Explicit cursors** are declared by the programmer for SELECT statements that return multiple rows:
```sql
DECLARE
    CURSOR c_emp IS SELECT * FROM employees;
    v_emp employees%ROWTYPE;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_emp;
        EXIT WHEN c_emp%NOTFOUND;
        -- process row
    END LOOP;
    CLOSE c_emp;
END;
```

There's also a **cursor FOR loop** which handles OPEN/FETCH/CLOSE automatically, and **REF cursors** (SYS_REFCURSOR) which are dynamic — the query is decided at runtime and can be passed between procedures.

---

## 5. What is the difference between a Stored Procedure and a Function?

**Answer:**
| Aspect | Procedure | Function |
|---|---|---|
| Return value | No (uses OUT parameters) | Must return exactly one value |
| Use in SQL | Cannot use in SELECT | Can use in SELECT |
| Purpose | Perform actions | Compute and return a value |
| Call syntax | `EXEC proc_name(args)` | `SELECT func_name(args) FROM dual` |

A function is for computation (like calculating tax), while a procedure is for actions (like processing an order). Functions should avoid side effects (DML) to be usable in SQL statements.

---

## 6. What are IN, OUT, and IN OUT parameters?

**Answer:**
- **IN** (default) — passes a value INTO the procedure. Read-only inside the procedure.
- **OUT** — the procedure sends a value BACK to the caller. Write-only (initially NULL inside).
- **IN OUT** — passes a value in AND can return a modified value back. Read-write.

```sql
PROCEDURE transfer(
    p_from_id IN NUMBER,     -- read-only input
    p_amount  IN NUMBER,     -- read-only input
    p_status  OUT VARCHAR2   -- output back to caller
);
```

---

## 7. What is a Package in PL/SQL?

**Answer:**
A package is a **container that groups related procedures, functions, types, and variables** together. It has two parts:

- **Specification** (header) — declares what's public (visible to callers)
- **Body** — contains the actual implementation, plus any private code

Benefits:
- **Encapsulation** — hide implementation details in the body
- **Modularity** — organize related code together
- **Performance** — entire package is loaded into memory on first call, so subsequent calls are faster
- **Overloading** — you can have multiple procedures with the same name but different parameters

---

## 8. What are Triggers? When would you use them?

**Answer:**
A trigger is a PL/SQL block that **automatically fires** when a specific event happens on a table — like INSERT, UPDATE, or DELETE.

Triggers can fire:
- **BEFORE** or **AFTER** the event
- **FOR EACH ROW** (row-level) or once per statement (statement-level)

I can access `:OLD` (value before change) and `:NEW` (value after change) in row-level triggers.

Common use cases: **audit logging** (who changed what and when), **enforcing complex business rules**, **auto-populating columns** (like setting `created_date`), and **maintaining derived data**.

⚠️ I'd be cautious with triggers — they can make debugging harder because they run implicitly, and excessive triggers slow down DML operations.

---

## 9. How does exception handling work in PL/SQL?

**Answer:**
PL/SQL handles errors in the EXCEPTION block. There are three types:

1. **Predefined exceptions** — Oracle-defined, like `NO_DATA_FOUND`, `TOO_MANY_ROWS`, `ZERO_DIVIDE`, `DUP_VAL_ON_INDEX`.

2. **User-defined exceptions** — I declare my own and raise them:
```sql
DECLARE
    e_insufficient_funds EXCEPTION;
BEGIN
    IF balance < amount THEN
        RAISE e_insufficient_funds;
    END IF;
EXCEPTION
    WHEN e_insufficient_funds THEN
        DBMS_OUTPUT.PUT_LINE('Not enough funds');
END;
```

3. **RAISE_APPLICATION_ERROR** — for custom error codes/messages sent back to the caller:
```sql
RAISE_APPLICATION_ERROR(-20001, 'Custom error message');
```

Exception propagation: if a block doesn't handle an exception, it propagates UP to the enclosing block, and ultimately back to the caller.

---

## 10. What is BULK COLLECT and FORALL? Why use them?

**Answer:**
They're PL/SQL features for **processing multiple rows at once** instead of one at a time, dramatically reducing **context switches** between the PL/SQL and SQL engines.

- **BULK COLLECT** fetches multiple rows into a collection in one shot:
```sql
SELECT * BULK COLLECT INTO v_employees FROM employees WHERE dept_id = 10;
```

- **FORALL** sends multiple DML statements in one shot:
```sql
FORALL i IN 1..v_ids.COUNT
    DELETE FROM employees WHERE id = v_ids(i);
```

Without bulk operations, processing 10,000 rows means 10,000 context switches. With BULK COLLECT + FORALL, it's essentially **one switch**. I've seen performance improvements of 10x or more on large datasets.

---

## 11. What is Dynamic SQL? When would you use it?

**Answer:**
Dynamic SQL is SQL that's constructed and executed **at runtime** using `EXECUTE IMMEDIATE`. It's used when the SQL statement isn't known at compile time — like when the table name, column name, or WHERE condition comes from user input or configuration.

```sql
EXECUTE IMMEDIATE 'SELECT count(*) FROM ' || v_table_name INTO v_count;
```

Use cases: generic utility procedures, dynamic reporting, DDL in PL/SQL.

⚠️ I always use **bind variables** to prevent SQL injection:
```sql
EXECUTE IMMEDIATE 'DELETE FROM employees WHERE id = :1' USING v_id;
```

---

## 12. What is the difference between a cursor FOR loop and an explicit cursor?

**Answer:**
A **cursor FOR loop** is syntactic sugar that handles OPEN, FETCH, and CLOSE automatically:
```sql
FOR rec IN (SELECT * FROM employees) LOOP
    DBMS_OUTPUT.PUT_LINE(rec.first_name);
END LOOP;
```

An **explicit cursor** gives me more control — I manually open, fetch, and close, which is useful when I need to process rows conditionally or pass the cursor to another procedure.

For most cases, I'd use the cursor FOR loop because it's cleaner and less error-prone (no risk of forgetting to close).

---

*Back to: [02-Oracle-PLSQL.md](../02-Oracle-PLSQL.md)*
