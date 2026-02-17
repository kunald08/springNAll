# JDBC & Networking — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. What is JDBC? Explain its architecture.

**Answer:**
JDBC stands for **Java Database Connectivity**. It's Java's standard API for connecting to and executing queries on relational databases.

The architecture has three layers:
1. **JDBC API** (`java.sql` package) — provides interfaces like `Connection`, `Statement`, `ResultSet`
2. **JDBC Driver** — a vendor-specific implementation that translates JDBC calls into the database's native protocol
3. **Database** — the actual database server

The beauty is that my application code only talks to the JDBC API (interfaces). By swapping the driver JAR, I can switch from Oracle to MySQL without changing a single line of application code.

---

## 2. What are the JDBC driver types?

**Answer:**
There are four types:
- **Type 1 (JDBC-ODBC Bridge)** — goes through ODBC. Removed in Java 8. Never used.
- **Type 2 (Native-API)** — uses database-specific native libraries. Platform-dependent.
- **Type 3 (Network Protocol)** — uses middleware server. Pure Java on client side.
- **Type 4 (Thin/Pure Java)** — communicates directly with the database using its network protocol. **This is what we use** — it's the fastest, platform-independent, and requires no native libraries.

Examples: Oracle Thin Driver, MySQL Connector/J, PostgreSQL JDBC Driver.

---

## 3. What is the difference between Statement and PreparedStatement?

**Answer:**
| Feature | Statement | PreparedStatement |
|---|---|---|
| SQL compilation | Compiled every time | Compiled once, reused |
| Parameters | String concatenation | Parameterized placeholders (?) |
| SQL injection | Vulnerable | Protected |
| Performance | Slower (re-parsing) | Faster (cached execution plan) |

I **always** use PreparedStatement because:
1. It prevents SQL injection — parameters are sent as data, never parsed as SQL
2. It's faster for repeated queries — the database caches the execution plan
3. It's more readable — no messy string concatenation

```java
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setInt(1, userId);   // Parameter is safely bound
ResultSet rs = ps.executeQuery();
```

---

## 4. What is SQL injection? How does PreparedStatement prevent it?

**Answer:**
SQL injection is when an attacker inserts malicious SQL through user input.

Example: If a login query uses string concatenation and the user enters `admin' --` as the username, the SQL becomes:
```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = ''
```
The `--` comments out the password check, and the attacker logs in as admin.

PreparedStatement prevents this because the SQL structure is sent to the database **first** and compiled. Then parameters are sent **separately as data**. The database never parses the parameter values as SQL code — `admin' --` is treated as a literal string, not SQL.

---

## 5. What is a ResultSet? How do you navigate it?

**Answer:**
A ResultSet holds the results of a SELECT query. By default, it's **forward-only** and **read-only** — I can only call `next()` to move forward.

```java
while (rs.next()) {
    String name = rs.getString("first_name");   // By column name (preferred)
    int id = rs.getInt(1);                       // By column index (1-based)
}
```

For scrollable ResultSets, I specify the type when creating the Statement:
```java
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY
);
```
Then I can use `previous()`, `first()`, `last()`, `absolute(n)`, etc.

---

## 6. What is a CallableStatement?

**Answer:**
CallableStatement is used to call **stored procedures and functions** in the database.

```java
CallableStatement cs = conn.prepareCall("{call get_employee(?, ?)}");
cs.setInt(1, 100);                          // IN parameter
cs.registerOutParameter(2, Types.VARCHAR);  // OUT parameter
cs.execute();
String name = cs.getString(2);              // Get OUT value
```

For functions: `{? = call get_salary(?)}` — the first `?` is the return value.

---

## 7. How do transactions work in JDBC?

**Answer:**
By default, JDBC is in **auto-commit mode** — every statement is committed immediately. For transactions, I disable auto-commit:

```java
conn.setAutoCommit(false);
try {
    // Execute multiple statements
    stmt1.executeUpdate(...);
    stmt2.executeUpdate(...);
    conn.commit();      // All succeed → commit
} catch (SQLException e) {
    conn.rollback();    // Any failure → rollback all
}
```

This ensures **atomicity** — either all operations succeed or none do. I can also use **Savepoints** to partially rollback within a transaction.

---

## 8. What is Socket Programming?

**Answer:**
Socket programming enables communication between two machines (or processes) over a network using **TCP** (reliable, ordered) or **UDP** (fast, unreliable).

A **socket** is an endpoint: IP address + port number.

The flow:
1. **Server** creates a `ServerSocket(port)` and calls `accept()` — blocks until a client connects
2. **Client** creates a `Socket(host, port)` — initiates a TCP 3-way handshake
3. Both sides get input/output streams to exchange data
4. Communication happens through streams (like reading/writing files)

```java
// Server
ServerSocket server = new ServerSocket(8080);
Socket client = server.accept();  // Blocks until client connects

// Client
Socket socket = new Socket("localhost", 8080);
```

---

## 9. What is the difference between TCP and UDP?

**Answer:**
| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Guaranteed delivery, ordering | No guarantees |
| Speed | Slower (overhead for reliability) | Faster |
| Use cases | Web (HTTP), email, file transfer | Streaming, gaming, DNS |

Java classes: `Socket`/`ServerSocket` for TCP, `DatagramSocket`/`DatagramPacket` for UDP.

---

## 10. What is connection pooling? Why is it important?

**Answer:**
Creating a database connection is **expensive** — it involves TCP handshake, authentication, and resource allocation. Connection pooling maintains a **pool of pre-created connections** that are reused.

Instead of: open → use → close (repeated), pooling does: borrow → use → return to pool.

Benefits: faster (no connection creation overhead), controlled resource usage (limit max connections), better scalability.

Libraries like **HikariCP** (fastest), **Apache DBCP**, or **C3P0** provide connection pooling. In Spring Boot, HikariCP is the default.

---

*Back to: [07-Java-JDBC-Networking.md](../07-Java-JDBC-Networking.md)*
