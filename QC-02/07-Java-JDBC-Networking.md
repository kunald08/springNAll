# Java JDBC & Networking — Database Connectivity & Sockets

## Table of Contents
1. [JDBC Architecture](#1-jdbc-architecture)
2. [JDBC Interfaces](#2-jdbc-interfaces)
3. [Driver Types & Registration](#3-driver-types--registration)
4. [Setting Up Database Driver](#4-setting-up-database-driver)
5. [Connection & Utility Class](#5-connection--utility-class)
6. [Statement Types](#6-statement-types)
7. [SQL Injection & PreparedStatement](#7-sql-injection--preparedstatement)
8. [CallableStatement (Stored Procedures)](#8-callablestatement)
9. [ResultSet — Navigating Rows](#9-resultset--navigating-rows)
10. [Transactions](#10-transactions)
11. [Java Networking & Sockets](#11-java-networking--sockets)

---

## 1. JDBC Architecture

**JDBC** (Java Database Connectivity) is Java's API for connecting to and executing queries on a database.

```
Your Java App
     │
     │ Uses JDBC API (java.sql.*)
     ▼
┌──────────────────────┐
│   JDBC API           │  Interfaces: Connection, Statement, ResultSet, etc.
│   (java.sql)         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   JDBC Driver        │  Implementation provided by database vendor
│   (e.g., Oracle,     │  Translates JDBC calls → database-specific protocol
│    MySQL, PostgreSQL)│
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Database           │  Oracle, MySQL, PostgreSQL, etc.
└──────────────────────┘

Think of it like a universal adapter:
- Your code speaks "JDBC language" (interfaces)
- The driver translates to "Oracle language" or "MySQL language"
- You can switch databases by just changing the driver!
```

---

## 2. JDBC Interfaces

```java
// The key interfaces in java.sql:

// 1. DriverManager — manages a list of database drivers
//    Entry point for getting a connection
DriverManager.getConnection(url, user, password);

// 2. Connection — represents a connection to the database
Connection conn = DriverManager.getConnection(url, user, pass);

// 3. Statement — sends SQL to the database
Statement stmt = conn.createStatement();

// 4. PreparedStatement — pre-compiled SQL (safer, faster)
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM emp WHERE id = ?");

// 5. CallableStatement — calls stored procedures
CallableStatement cstmt = conn.prepareCall("{call my_proc(?, ?)}");

// 6. ResultSet — holds the results of a query
ResultSet rs = stmt.executeQuery("SELECT * FROM emp");

// 7. DataSource — alternative to DriverManager (used in enterprise apps)
//    Supports connection pooling and distributed transactions
```

### Hierarchy

```
     DriverManager
          │
          │ getConnection()
          ▼
      Connection
       │     │      │
       ▼     ▼      ▼
   Statement  PreparedStatement  CallableStatement
       │            │                  │
       └────────────┴──────────────────┘
                    │
                    ▼
                 ResultSet
```

---

## 3. Driver Types & Registration

### Four Types of JDBC Drivers

```
Type 1: JDBC-ODBC Bridge
  Java → JDBC API → ODBC → Database
  - Slowest, requires ODBC installed
  - Removed in Java 8
  - Never used anymore

Type 2: Native-API Driver (Partially Java)
  Java → JDBC API → Native C/C++ library → Database
  - Faster than Type 1
  - Requires native library on client machine
  - Platform dependent

Type 3: Network Protocol Driver (Middleware)
  Java → JDBC API → Middleware Server → Database
  - Pure Java on client side
  - Middleware handles translation
  - Good for internet applications

Type 4: Thin Driver (Pure Java) ← MOST COMMON / PREFERRED
  Java → JDBC API → Direct to Database via network protocol
  - 100% pure Java
  - Fastest (no extra layers)
  - No native libraries needed
  - Database vendor provides the driver
  - Example: Oracle Thin Driver, MySQL Connector/J
```

### Driver Registration

```java
// Old way (before JDBC 4.0):
Class.forName("oracle.jdbc.driver.OracleDriver");  // Manually load driver class

// New way (JDBC 4.0+ / Java 6+):
// Drivers are auto-discovered via ServiceLoader mechanism!
// Just include the driver JAR in your classpath — done!

// The driver JAR contains:
//   META-INF/services/java.sql.Driver
// with the fully qualified driver class name inside the file
```

---

## 4. Setting Up Database Driver

### Maven Dependency

```xml
<!-- Oracle -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc11</artifactId>
    <version>23.3.0.23.09</version>
</dependency>

<!-- MySQL -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.2.0</version>
</dependency>

<!-- PostgreSQL -->
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.7.1</version>
</dependency>
```

### JDBC URL Format

```
General:   jdbc:<subprotocol>:<subname>

Oracle:    jdbc:oracle:thin:@<host>:<port>:<SID>
           jdbc:oracle:thin:@<host>:<port>/<service_name>
           jdbc:oracle:thin:@//localhost:1521/XEPDB1

MySQL:     jdbc:mysql://localhost:3306/mydb

PostgreSQL: jdbc:postgresql://localhost:5432/mydb
```

---

## 5. Connection & Utility Class

### Basic Connection

```java
import java.sql.*;

public class JDBCDemo {
    public static void main(String[] args) {
        String url = "jdbc:oracle:thin:@//localhost:1521/XEPDB1";
        String user = "hr";
        String password = "hr";

        // Try-with-resources auto-closes the connection
        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            System.out.println("Connected!");
            System.out.println("Database: " + conn.getMetaData().getDatabaseProductName());
        } catch (SQLException e) {
            System.err.println("Connection failed: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### Utility Class Pattern

```java
public class ConnectionUtil {
    
    // Store config — in real apps, use properties file or environment variables
    private static final String URL = "jdbc:oracle:thin:@//localhost:1521/XEPDB1";
    private static final String USER = "hr";
    private static final String PASSWORD = "hr";
    
    // Private constructor — utility class, no instances needed
    private ConnectionUtil() {}
    
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }
}

// Usage:
try (Connection conn = ConnectionUtil.getConnection()) {
    // do work
}
```

### Using Properties File (Better)

```java
// db.properties file:
// db.url=jdbc:oracle:thin:@//localhost:1521/XEPDB1
// db.user=hr
// db.password=hr

public class ConnectionUtil {
    private static final Properties props = new Properties();
    
    static {
        try (InputStream in = ConnectionUtil.class
                .getClassLoader()
                .getResourceAsStream("db.properties")) {
            props.load(in);
        } catch (IOException e) {
            throw new RuntimeException("Cannot load db.properties", e);
        }
    }
    
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(
            props.getProperty("db.url"),
            props.getProperty("db.user"),
            props.getProperty("db.password")
        );
    }
}
```

---

## 6. Statement Types

### Statement (Simple)

```java
try (Connection conn = ConnectionUtil.getConnection();
     Statement stmt = conn.createStatement()) {
    
    // Execute a query (SELECT) — returns ResultSet
    ResultSet rs = stmt.executeQuery("SELECT * FROM employees");
    while (rs.next()) {
        System.out.println(rs.getInt("employee_id") + " " + rs.getString("first_name"));
    }
    
    // Execute an update (INSERT/UPDATE/DELETE) — returns affected row count
    int rows = stmt.executeUpdate("UPDATE employees SET salary = 5000 WHERE id = 1");
    System.out.println(rows + " rows updated");
    
    // Execute any SQL — returns true if ResultSet, false if update count
    boolean isResultSet = stmt.execute("CREATE TABLE test (id NUMBER)");
}
```

### PreparedStatement (Parameterized — PREFERRED)

```java
String sql = "SELECT * FROM employees WHERE department_id = ? AND salary > ?";

try (Connection conn = ConnectionUtil.getConnection();
     PreparedStatement pstmt = conn.prepareStatement(sql)) {
    
    // Set parameters (1-based index!)
    pstmt.setInt(1, 10);         // First ? → department_id = 10
    pstmt.setDouble(2, 5000);   // Second ? → salary > 5000
    
    ResultSet rs = pstmt.executeQuery();
    while (rs.next()) {
        System.out.println(rs.getString("first_name"));
    }
}
```

### Why PreparedStatement > Statement

```
1. SQL INJECTION PROTECTION (most important!)
   Statement: vulnerable to injection attacks
   PreparedStatement: parameters are escaped automatically

2. PERFORMANCE
   Statement: SQL is parsed and compiled EVERY TIME
   PreparedStatement: SQL is parsed and compiled ONCE, then reused

   Database:
     Statement:           PreparedStatement:
     Parse SQL      →     Parse SQL (first time only)
     Compile        →     Compile (first time only)
     Execute        →     Execute (with new params)
     Parse SQL      →     Execute (with new params)
     Compile        →     Execute (with new params)
     Execute        →     ...much faster!

3. READABILITY
   Statement: messy string concatenation
   PreparedStatement: clean parameterized SQL
```

---

## 7. SQL Injection & PreparedStatement

### What Is SQL Injection?

```java
// VULNERABLE code using Statement:
String username = request.getParameter("username");   // User input!
String password = request.getParameter("password");   // User input!

String sql = "SELECT * FROM users WHERE username = '" + username 
           + "' AND password = '" + password + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);

// If user enters:  username = admin' --
// The SQL becomes:
// SELECT * FROM users WHERE username = 'admin' --' AND password = ''
// The -- comments out the password check! Attacker logs in as admin!

// If user enters:  username = ' OR '1'='1
// The SQL becomes:
// SELECT * FROM users WHERE username = '' OR '1'='1' AND password = ''
// 1=1 is always true → returns ALL users!

// Even worse:  username = '; DROP TABLE users; --
// Deletes the entire table!
```

### Prevention with PreparedStatement

```java
// SAFE code using PreparedStatement:
String sql = "SELECT * FROM users WHERE username = ? AND password = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, username);   // Automatically escaped!
pstmt.setString(2, password);   // Automatically escaped!
ResultSet rs = pstmt.executeQuery();

// If user enters: admin' --
// PreparedStatement treats it as a LITERAL STRING "admin' --"
// The database looks for a user literally named "admin' --" → not found → safe!

// Under the hood:
// 1. SQL structure is sent to DB and compiled: SELECT * FROM users WHERE username = ? AND password = ?
// 2. Parameters are sent SEPARATELY as data, NOT as SQL code
// 3. The database NEVER parses the parameters as SQL!
```

---

## 8. CallableStatement

Used to call **stored procedures** and **functions** in the database.

```java
// Oracle stored procedure:
// CREATE PROCEDURE get_emp_name(p_id IN NUMBER, p_name OUT VARCHAR2) AS
// BEGIN
//     SELECT first_name INTO p_name FROM employees WHERE employee_id = p_id;
// END;

// Java code to call it:
String sql = "{call get_emp_name(?, ?)}";
try (Connection conn = ConnectionUtil.getConnection();
     CallableStatement cstmt = conn.prepareCall(sql)) {
    
    // Set IN parameter
    cstmt.setInt(1, 100);
    
    // Register OUT parameter (must specify type!)
    cstmt.registerOutParameter(2, Types.VARCHAR);
    
    // Execute
    cstmt.execute();
    
    // Get OUT parameter value
    String name = cstmt.getString(2);
    System.out.println("Name: " + name);
}

// Calling a function:
// CREATE FUNCTION get_salary(p_id NUMBER) RETURN NUMBER AS
// BEGIN
//     ...
// END;

String sql = "{? = call get_salary(?)}";   // Note: ? = call for functions
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.registerOutParameter(1, Types.NUMERIC);  // Return value
cstmt.setInt(2, 100);                          // Input parameter
cstmt.execute();
double salary = cstmt.getDouble(1);
```

---

## 9. ResultSet — Navigating Rows

### Basic ResultSet

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// next() moves to next row. Returns false when no more rows.
while (rs.next()) {
    // Get by column INDEX (1-based!)
    int id = rs.getInt(1);
    String name = rs.getString(2);
    
    // Get by column NAME (preferred — more readable)
    int id2 = rs.getInt("employee_id");
    String name2 = rs.getString("first_name");
    double salary = rs.getDouble("salary");
    Date hireDate = rs.getDate("hire_date");
    
    // Check for NULL values
    double commission = rs.getDouble("commission_pct");
    if (rs.wasNull()) {
        System.out.println("Commission is NULL");
    }
}
```

### Scrollable and Updatable ResultSet

```java
// Default ResultSet: forward-only, read-only
// To get scrollable/updatable, specify when creating Statement:
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,  // Can scroll forward AND backward
    ResultSet.CONCUR_UPDATABLE           // Can update rows through ResultSet
);

ResultSet rs = stmt.executeQuery("SELECT * FROM employees");

// Navigation methods:
rs.next();           // Move to next row
rs.previous();       // Move to previous row
rs.first();          // Move to first row
rs.last();           // Move to last row
rs.absolute(5);      // Move to 5th row
rs.relative(-2);     // Move 2 rows backward
rs.beforeFirst();    // Move before first row
rs.afterLast();      // Move after last row

// Position checking:
rs.isFirst();
rs.isLast();
rs.isBeforeFirst();
rs.isAfterLast();
rs.getRow();         // Current row number

// ResultSet types:
// TYPE_FORWARD_ONLY       — can only call next() (default)
// TYPE_SCROLL_INSENSITIVE — scrollable, doesn't see DB changes after creation
// TYPE_SCROLL_SENSITIVE   — scrollable, sees DB changes (slower)

// Concurrency modes:
// CONCUR_READ_ONLY   — can only read (default)
// CONCUR_UPDATABLE   — can update/insert/delete through ResultSet
```

---

## 10. Transactions

By default, JDBC is in **auto-commit** mode — every statement is committed immediately.

```java
Connection conn = ConnectionUtil.getConnection();

try {
    // Disable auto-commit — start a transaction
    conn.setAutoCommit(false);
    
    // Execute multiple statements as ONE atomic transaction
    PreparedStatement transfer = conn.prepareStatement(
        "UPDATE accounts SET balance = balance + ? WHERE id = ?"
    );
    
    // Debit from account 1
    transfer.setDouble(1, -500.00);
    transfer.setInt(2, 1);
    transfer.executeUpdate();
    
    // Credit to account 2
    transfer.setDouble(1, 500.00);
    transfer.setInt(2, 2);
    transfer.executeUpdate();
    
    // If both succeed → commit
    conn.commit();
    System.out.println("Transfer successful!");
    
} catch (SQLException e) {
    // If anything fails → rollback everything
    conn.rollback();
    System.err.println("Transfer failed, rolled back: " + e.getMessage());
    
} finally {
    conn.setAutoCommit(true);  // Reset to default
    conn.close();
}
```

### Savepoints

```java
conn.setAutoCommit(false);

stmt.executeUpdate("INSERT INTO orders ...");
Savepoint sp = conn.setSavepoint("after_order");

try {
    stmt.executeUpdate("INSERT INTO order_items ...");
} catch (SQLException e) {
    conn.rollback(sp);          // Roll back to savepoint only
    // The order INSERT is still intact!
}

conn.commit();                   // Commit whatever wasn't rolled back
```

---

## 11. Java Networking & Sockets

### Networking Basics

```
Client-Server Model:
┌───────────┐                      ┌───────────┐
│  Client   │ ── Request ───────▶ │  Server   │
│ (Browser) │ ◀─ Response ──────  │ (Web App) │
└───────────┘                      └───────────┘

IP Address: Identifies a machine (192.168.1.100)
Port:       Identifies a service on that machine (80 for HTTP, 3306 for MySQL)
Socket:     Combination of IP + Port = endpoint for communication
Protocol:   Rules for communication (TCP = reliable, UDP = fast but unreliable)
```

### Socket Programming — TCP

```java
// SERVER side — listens for connections
import java.net.*;
import java.io.*;

public class SimpleServer {
    public static void main(String[] args) throws IOException {
        // Create server socket on port 8080
        try (ServerSocket serverSocket = new ServerSocket(8080)) {
            System.out.println("Server listening on port 8080...");
            
            // accept() BLOCKS until a client connects
            try (Socket clientSocket = serverSocket.accept();
                 PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
                 BufferedReader in = new BufferedReader(
                     new InputStreamReader(clientSocket.getInputStream()))) {
                
                System.out.println("Client connected: " + clientSocket.getInetAddress());
                
                // Read message from client
                String message = in.readLine();
                System.out.println("Client says: " + message);
                
                // Send response
                out.println("Hello from server! You said: " + message);
            }
        }
    }
}
```

```java
// CLIENT side — connects to server
public class SimpleClient {
    public static void main(String[] args) throws IOException {
        // Connect to server
        try (Socket socket = new Socket("localhost", 8080);
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
             BufferedReader in = new BufferedReader(
                 new InputStreamReader(socket.getInputStream()))) {
            
            // Send message to server
            out.println("Hello, Server!");
            
            // Read response from server
            String response = in.readLine();
            System.out.println("Server says: " + response);
        }
    }
}
```

### How Sockets Work Under the Hood

```
1. Server creates ServerSocket(8080)
   → OS binds port 8080 to this process
   → Internally creates a TCP listen socket

2. Server calls accept()
   → Thread blocks, waiting for TCP SYN packet

3. Client creates Socket("localhost", 8080)
   → OS performs TCP 3-way handshake:
     Client → SYN → Server
     Client ← SYN-ACK ← Server
     Client → ACK → Server

4. accept() returns a new Socket for this specific client
   → Server can now talk to this client via streams

5. Data flows as byte streams over TCP
   → TCP guarantees delivery, ordering, no duplicates
   → Under the hood: data is split into segments, checksummed, 
     acknowledged, retransmitted if lost
```

### Multi-Client Server

```java
public class MultiClientServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("Server started on port 8080");
        
        // Using thread pool for handling multiple clients
        ExecutorService pool = Executors.newFixedThreadPool(10);
        
        while (true) {
            Socket clientSocket = serverSocket.accept();  // Wait for client
            
            // Handle each client in a separate thread
            pool.submit(() -> handleClient(clientSocket));
        }
    }
    
    private static void handleClient(Socket socket) {
        try (BufferedReader in = new BufferedReader(
                 new InputStreamReader(socket.getInputStream()));
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true)) {
            
            String line;
            while ((line = in.readLine()) != null) {
                System.out.println("Received: " + line);
                out.println("Echo: " + line);
                
                if (line.equalsIgnoreCase("bye")) break;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### java.net Useful Classes

```java
// InetAddress — represents an IP address
InetAddress addr = InetAddress.getByName("www.google.com");
System.out.println(addr.getHostAddress());  // "142.250.xx.xx"
System.out.println(addr.getHostName());      // "www.google.com"
InetAddress local = InetAddress.getLocalHost();

// URL — represents a web address
URL url = new URL("https://api.example.com/users?page=1");
System.out.println(url.getProtocol());  // "https"
System.out.println(url.getHost());      // "api.example.com"
System.out.println(url.getPath());      // "/users"
System.out.println(url.getQuery());     // "page=1"

// HttpURLConnection — make HTTP requests (basic)
URL url = new URL("https://api.example.com/users");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");

int responseCode = conn.getResponseCode();
BufferedReader reader = new BufferedReader(
    new InputStreamReader(conn.getInputStream()));
String line;
while ((line = reader.readLine()) != null) {
    System.out.println(line);
}

// HttpClient (Java 11+) — modern HTTP client
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users"))
    .GET()
    .build();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.statusCode());
System.out.println(response.body());
```

---

*Previous: [06-Java-Advanced.md](06-Java-Advanced.md)*
*Next: [08-Design-Patterns-SOLID.md](08-Design-Patterns-SOLID.md)*
