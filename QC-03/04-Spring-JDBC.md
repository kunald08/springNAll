# Spring JDBC & Transactions - Complete In-Depth Guide

## Table of Contents
- [1. The Problem: Plain JDBC is Painful](#1-the-problem-plain-jdbc-is-painful)
- [2. JdbcTemplate](#2-jdbctemplate)
- [3. NamedParameterJdbcTemplate](#3-namedparameterjdbctemplate)
- [4. RowMapper](#4-rowmapper)
- [5. @Transactional Annotation](#5-transactional-annotation)
- [6. Transaction Propagation](#6-transaction-propagation)

---

## 1. The Problem: Plain JDBC is Painful

### Traditional JDBC Code (Without Spring)

```java
// This is what you'd write WITHOUT Spring — look how messy it is!
public User findUserById(Long id) {
    Connection connection = null;
    PreparedStatement statement = null;
    ResultSet resultSet = null;
    
    try {
        // Step 1: Get connection
        connection = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/mydb", "root", "password"
        );
        
        // Step 2: Create statement
        String sql = "SELECT * FROM users WHERE id = ?";
        statement = connection.prepareStatement(sql);
        statement.setLong(1, id);
        
        // Step 3: Execute query
        resultSet = statement.executeQuery();
        
        // Step 4: Map results
        if (resultSet.next()) {
            User user = new User();
            user.setId(resultSet.getLong("id"));
            user.setName(resultSet.getString("name"));
            user.setEmail(resultSet.getString("email"));
            return user;
        }
        return null;
        
    } catch (SQLException e) {
        // Step 5: Handle exceptions (checked exceptions — forced to catch)
        throw new RuntimeException("Database error", e);
        
    } finally {
        // Step 6: Close resources (DON'T FORGET or you get memory leaks!)
        try {
            if (resultSet != null) resultSet.close();
            if (statement != null) statement.close();
            if (connection != null) connection.close();
        } catch (SQLException e) {
            // Even closing can throw exceptions! 😩
        }
    }
}
```

**Problems:**
1. Too much **boilerplate code** (repetitive code)
2. **Resource management** — must close everything manually
3. **Exception handling** — forced to catch checked `SQLException`
4. **Opening/closing connections** every time is expensive
5. Same pattern repeated for every database operation

---

## 2. JdbcTemplate

### What is JdbcTemplate?

`JdbcTemplate` is Spring's solution to simplify JDBC operations. It handles:
- Connection management (open/close)
- Statement creation and execution
- ResultSet processing
- Exception translation (converts `SQLException` to unchecked `DataAccessException`)
- Resource cleanup

### The Same Query with JdbcTemplate

```java
// THIS replaces all that boilerplate code above!
public User findUserById(Long id) {
    String sql = "SELECT * FROM users WHERE id = ?";
    return jdbcTemplate.queryForObject(sql, new UserRowMapper(), id);
}
```

**From 40+ lines to 3 lines!**

### Setting Up JdbcTemplate

#### Step 1: Add Dependencies (pom.xml)

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

#### Step 2: Configure Database (application.properties)

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

#### Step 3: Use JdbcTemplate

```java
@Repository
public class UserRepository {
    
    private final JdbcTemplate jdbcTemplate;
    
    // Spring auto-creates JdbcTemplate bean and injects it
    @Autowired
    public UserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    // ============ INSERT ============
    public int saveUser(User user) {
        String sql = "INSERT INTO users (name, email, age) VALUES (?, ?, ?)";
        return jdbcTemplate.update(sql, user.getName(), user.getEmail(), user.getAge());
        // Returns number of rows affected (1 for successful insert)
    }
    
    // ============ UPDATE ============
    public int updateUser(User user) {
        String sql = "UPDATE users SET name = ?, email = ? WHERE id = ?";
        return jdbcTemplate.update(sql, user.getName(), user.getEmail(), user.getId());
    }
    
    // ============ DELETE ============
    public int deleteUser(Long id) {
        String sql = "DELETE FROM users WHERE id = ?";
        return jdbcTemplate.update(sql, id);
    }
    
    // ============ QUERY SINGLE ROW ============
    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new UserRowMapper(), id);
    }
    
    // ============ QUERY MULTIPLE ROWS ============
    public List<User> findAll() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, new UserRowMapper());
    }
    
    // ============ QUERY SINGLE VALUE ============
    public int countUsers() {
        String sql = "SELECT COUNT(*) FROM users";
        return jdbcTemplate.queryForObject(sql, Integer.class);
    }
    
    // ============ QUERY FOR A LIST OF SINGLE VALUES ============
    public List<String> findAllEmails() {
        String sql = "SELECT email FROM users";
        return jdbcTemplate.queryForList(sql, String.class);
    }
    
    // ============ BATCH INSERT ============
    public int[] batchInsert(List<User> users) {
        String sql = "INSERT INTO users (name, email, age) VALUES (?, ?, ?)";
        return jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {
            @Override
            public void setValues(PreparedStatement ps, int i) throws SQLException {
                User user = users.get(i);
                ps.setString(1, user.getName());
                ps.setString(2, user.getEmail());
                ps.setInt(3, user.getAge());
            }
            
            @Override
            public int getBatchSize() {
                return users.size();
            }
        });
    }
}
```

### Under the Hood: What JdbcTemplate Does for You

```
When you call: jdbcTemplate.update(sql, name, email, id)

Step 1: Gets a Connection from the DataSource (connection pool)
Step 2: Creates a PreparedStatement with the SQL
Step 3: Sets parameters (name → ?, email → ?, id → ?)
Step 4: Executes the statement
Step 5: Gets the result
Step 6: Closes the PreparedStatement
Step 7: Returns the Connection to the pool
Step 8: Translates any SQLException to DataAccessException
Step 9: Returns the result to you

All of this happens BEHIND THE SCENES — you just write the SQL!
```

### JdbcTemplate Methods Summary

| Method | Use When |
|--------|----------|
| `update(sql, args...)` | INSERT, UPDATE, DELETE |
| `query(sql, rowMapper)` | SELECT multiple rows |
| `queryForObject(sql, rowMapper, args...)` | SELECT single row |
| `queryForObject(sql, type, args...)` | SELECT single value (COUNT, MAX) |
| `queryForList(sql, type)` | SELECT column as list |
| `batchUpdate(sql, batchSetter)` | Bulk INSERT/UPDATE |
| `execute(sql)` | DDL statements (CREATE TABLE) |

---

## 3. NamedParameterJdbcTemplate

### The Problem with JdbcTemplate

With `JdbcTemplate`, you use `?` placeholders. When you have many parameters, it gets confusing:

```java
// Which ? is which? Easy to mix up the order!
String sql = "INSERT INTO users (name, email, age, city, phone) VALUES (?, ?, ?, ?, ?)";
jdbcTemplate.update(sql, name, email, age, city, phone);
// If you swap age and city by accident — silent bug! 😱
```

### The Solution: Named Parameters

`NamedParameterJdbcTemplate` uses **named placeholders** (`:name`, `:email`) instead of `?`:

```java
// Much clearer! Each parameter has a name
String sql = "INSERT INTO users (name, email, age) VALUES (:name, :email, :age)";
```

### Setup and Usage

```java
@Repository
public class UserRepository {
    
    private final NamedParameterJdbcTemplate namedTemplate;
    
    @Autowired
    public UserRepository(NamedParameterJdbcTemplate namedTemplate) {
        this.namedTemplate = namedTemplate;
    }
    
    // ============ Using MapSqlParameterSource ============
    public int saveUser(User user) {
        String sql = "INSERT INTO users (name, email, age) VALUES (:name, :email, :age)";
        
        MapSqlParameterSource params = new MapSqlParameterSource();
        params.addValue("name", user.getName());
        params.addValue("email", user.getEmail());
        params.addValue("age", user.getAge());
        
        return namedTemplate.update(sql, params);
    }
    
    // ============ Using BeanPropertySqlParameterSource ============
    // Automatically maps bean properties to named parameters!
    public int saveUserFromBean(User user) {
        String sql = "INSERT INTO users (name, email, age) VALUES (:name, :email, :age)";
        
        // Spring reads user.getName(), user.getEmail(), user.getAge()
        // and maps them to :name, :email, :age automatically
        BeanPropertySqlParameterSource params = new BeanPropertySqlParameterSource(user);
        
        return namedTemplate.update(sql, params);
    }
    
    // ============ Using Simple Map ============
    public User findByEmail(String email) {
        String sql = "SELECT * FROM users WHERE email = :email";
        
        Map<String, Object> params = Map.of("email", email);
        
        return namedTemplate.queryForObject(sql, params, new UserRowMapper());
    }
    
    // ============ Complex Query ============
    public List<User> findByAgeRange(int minAge, int maxAge) {
        String sql = "SELECT * FROM users WHERE age BETWEEN :minAge AND :maxAge ORDER BY name";
        
        MapSqlParameterSource params = new MapSqlParameterSource();
        params.addValue("minAge", minAge);
        params.addValue("maxAge", maxAge);
        
        return namedTemplate.query(sql, params, new UserRowMapper());
    }
    
    // ============ IN Clause (List of values) ============
    public List<User> findByIds(List<Long> ids) {
        String sql = "SELECT * FROM users WHERE id IN (:ids)";
        
        MapSqlParameterSource params = new MapSqlParameterSource();
        params.addValue("ids", ids);  // Handles List automatically!
        
        return namedTemplate.query(sql, params, new UserRowMapper());
    }
}
```

### JdbcTemplate vs NamedParameterJdbcTemplate

| Feature | JdbcTemplate | NamedParameterJdbcTemplate |
|---------|-------------|---------------------------|
| Placeholders | `?` (positional) | `:name` (named) |
| Readability | Low for many params | High — params are named |
| Error-prone | Order matters | Name-based, order doesn't matter |
| IN clause | Complex | Simple (pass a List) |
| When to use | Simple queries | Complex queries with many params |

---

## 4. RowMapper

### What is RowMapper?

A `RowMapper` converts a **database row** (ResultSet) into a **Java object**. It tells Spring how to map each column to a field in your class.

### Basic RowMapper

```java
// Your model class
public class User {
    private Long id;
    private String name;
    private String email;
    private int age;
    
    // Constructors, getters, setters...
    public User() {}
    
    public User(Long id, String name, String email, int age) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.age = age;
    }
    
    // Getters and setters...
}

// RowMapper implementation
public class UserRowMapper implements RowMapper<User> {
    
    @Override
    public User mapRow(ResultSet rs, int rowNum) throws SQLException {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setName(rs.getString("name"));
        user.setEmail(rs.getString("email"));
        user.setAge(rs.getInt("age"));
        return user;
    }
}
```

### Using RowMapper

```java
@Repository
public class UserRepository {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    // Way 1: Use a separate RowMapper class
    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new UserRowMapper(), id);
    }
    
    // Way 2: Use a lambda (inline)
    public User findByEmail(String email) {
        String sql = "SELECT * FROM users WHERE email = ?";
        return jdbcTemplate.queryForObject(sql, (rs, rowNum) -> {
            User user = new User();
            user.setId(rs.getLong("id"));
            user.setName(rs.getString("name"));
            user.setEmail(rs.getString("email"));
            user.setAge(rs.getInt("age"));
            return user;
        }, email);
    }
    
    // Way 3: Use BeanPropertyRowMapper (automatic mapping)
    // Column names must match field names (or use aliases)
    public List<User> findAll() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(User.class));
        // Automatically maps: id→id, name→name, email→email, age→age
    }
}
```

### Under the Hood: How RowMapper Works

```
Database returns ResultSet:
┌────┬─────────┬───────────────────┬─────┐
│ id │  name   │      email        │ age │
├────┼─────────┼───────────────────┼─────┤
│  1 │ Kunal   │ kunal@email.com   │  25 │
│  2 │ Priya   │ priya@email.com   │  28 │
│  3 │ Rahul   │ rahul@email.com   │  22 │
└────┴─────────┴───────────────────┴─────┘

For each row, Spring calls: rowMapper.mapRow(resultSet, rowNumber)

Row 1: mapRow(rs, 0) → User{id=1, name="Kunal", email="kunal@email.com", age=25}
Row 2: mapRow(rs, 1) → User{id=2, name="Priya", email="priya@email.com", age=28}
Row 3: mapRow(rs, 2) → User{id=3, name="Rahul", email="rahul@email.com", age=22}

Spring collects all results into a List<User>
```

### BeanPropertyRowMapper (Auto-Mapping)

```java
// BeanPropertyRowMapper automatically maps columns to fields
// Match rules:
//   Column: user_name  →  Field: userName  (underscore to camelCase)
//   Column: email      →  Field: email     (exact match)
//   Column: ID         →  Field: id        (case-insensitive)

public List<User> findAll() {
    return jdbcTemplate.query(
        "SELECT * FROM users", 
        new BeanPropertyRowMapper<>(User.class)
    );
}
// No manual mapping needed! Spring figures it out automatically.
```

---

## 5. @Transactional Annotation

### What is a Transaction?

A transaction is a group of database operations that must **ALL succeed or ALL fail**. There is no partial success.

### Classic Example: Money Transfer

```
Transfer $100 from Account A to Account B:

Step 1: Deduct $100 from Account A    → UPDATE accounts SET balance = balance - 100 WHERE id = 'A'
Step 2: Add $100 to Account B         → UPDATE accounts SET balance = balance + 100 WHERE id = 'B'

What if Step 1 succeeds but Step 2 fails (server crash)?
→ $100 vanished! Account A lost money, Account B didn't get it!

SOLUTION: Transaction
→ If Step 2 fails, ROLLBACK Step 1 (undo it)
→ Money goes back to Account A
→ Data integrity preserved ✅
```

### ACID Properties

| Property | Meaning | Example |
|----------|---------|---------|
| **A**tomicity | All or nothing | Both debit and credit happen, or neither |
| **C**onsistency | Data stays valid | Total money stays the same |
| **I**solation | Transactions don't interfere | Two transfers at same time don't mess up |
| **D**urability | Changes persist | Once committed, data is saved even if server crashes |

### Using @Transactional

```java
@Service
public class BankService {
    
    @Autowired
    private AccountRepository accountRepository;
    
    @Transactional  // ← This makes the entire method a SINGLE transaction
    public void transferMoney(Long fromId, Long toId, double amount) {
        
        // Step 1: Deduct from sender
        Account sender = accountRepository.findById(fromId);
        if (sender.getBalance() < amount) {
            throw new InsufficientFundsException("Not enough balance!");
        }
        sender.setBalance(sender.getBalance() - amount);
        accountRepository.save(sender);
        
        // Step 2: Add to receiver
        Account receiver = accountRepository.findById(toId);
        receiver.setBalance(receiver.getBalance() + amount);
        accountRepository.save(receiver);
        
        // If ANY exception occurs, BOTH operations are ROLLED BACK
        // Money is safe! ✅
    }
}
```

### Under the Hood: How @Transactional Works

```
When Spring sees @Transactional:

1. Spring creates a PROXY around your service class
2. Before the method runs:
   → Opens a database connection
   → Starts a transaction (BEGIN TRANSACTION)
3. Method runs normally:
   → All SQL operations use the SAME connection and SAME transaction
4. After method completes:
   → If NO exception: COMMIT (save all changes permanently)
   → If RuntimeException: ROLLBACK (undo all changes)

Your Service:                    Proxy (created by Spring):
┌──────────────┐                ┌──────────────────────────┐
│ transferMoney│  → REPLACED BY  │ BEGIN TRANSACTION        │
│              │                │ → call transferMoney()   │
│              │                │ → if success: COMMIT     │
│              │                │ → if error: ROLLBACK     │
└──────────────┘                └──────────────────────────┘
```

### @Transactional Parameters

```java
@Transactional(
    // When to ROLLBACK
    rollbackFor = Exception.class,        // Rollback for any Exception
    noRollbackFor = EmailException.class,  // Don't rollback for this exception
    
    // Isolation level
    isolation = Isolation.READ_COMMITTED,
    
    // Propagation behavior
    propagation = Propagation.REQUIRED,
    
    // Timeout (in seconds)
    timeout = 30,
    
    // Read-only optimization
    readOnly = false
)
public void transferMoney(Long from, Long to, double amount) {
    // ...
}
```

### Important Rules for @Transactional

```
Rule 1: Only works on PUBLIC methods
  ✅ @Transactional public void transfer() { }
  ❌ @Transactional private void transfer() { }   // Won't work!
  
Rule 2: Default rollback on RuntimeException only
  ✅ Rolls back on: NullPointerException, IllegalArgumentException
  ❌ Does NOT rollback on: IOException, SQLException (checked exceptions)
  Fix: @Transactional(rollbackFor = Exception.class)

Rule 3: Self-invocation doesn't work
  // This WON'T create a transaction!
  public class MyService {
      public void methodA() {
          methodB();  // Direct call — no proxy involved!
      }
      
      @Transactional
      public void methodB() { }
  }
  // Because the proxy only intercepts EXTERNAL calls, not internal ones.

Rule 4: Class must be a Spring bean (@Service, @Component, etc.)
```

### Read-Only Transactions

```java
@Service
public class ReportService {
    
    @Transactional(readOnly = true)  // Optimization for read-only queries
    public List<User> generateReport() {
        // Hibernate skips dirty checking (faster!)
        // Database may use read-only optimizations
        return userRepository.findAll();
    }
}
```

---

## 6. Transaction Propagation

### What is Transaction Propagation?

When a transactional method calls another transactional method, what happens? **Propagation** defines this behavior.

```java
@Service
public class OrderService {
    
    @Transactional
    public void placeOrder(Order order) {
        saveOrder(order);
        paymentService.processPayment(order);  // This is also @Transactional
        // Should processPayment use the SAME transaction or a NEW one?
    }
}
```

### Propagation Types

#### REQUIRED (Default)

```java
@Transactional(propagation = Propagation.REQUIRED)
public void methodA() {
    // If a transaction exists → USE IT
    // If NO transaction exists → CREATE a new one
}
```

```
Scenario 1: methodA() called WITHOUT existing transaction
  → Creates NEW transaction
  → methodA runs inside it

Scenario 2: methodA() called WITH existing transaction (from caller)
  → JOINS the existing transaction
  → Both methods share same transaction
  → If methodA fails → ENTIRE transaction rolls back
```

#### REQUIRES_NEW

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void audit(String message) {
    // ALWAYS creates a NEW transaction
    // Suspends the outer transaction (if any)
    auditRepository.save(new AuditLog(message));
}
```

```
Scenario: processPayment() has REQUIRES_NEW
  
  placeOrder() starts Transaction 1
    ├── saveOrder()          → uses Transaction 1
    ├── processPayment()     → SUSPENDS Transaction 1
    │                        → Creates NEW Transaction 2
    │                        → Runs in Transaction 2
    │                        → COMMITS Transaction 2
    │                        → RESUMES Transaction 1
    └── continues...         → uses Transaction 1

If processPayment fails → Transaction 2 rolls back
                        → Transaction 1 can still COMMIT (or not)
```

#### SUPPORTS

```java
@Transactional(propagation = Propagation.SUPPORTS)
public void readData() {
    // If a transaction exists → USE IT
    // If NO transaction exists → Run WITHOUT transaction (that's fine)
}
```

#### NOT_SUPPORTED

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
public void sendEmail() {
    // Suspends any existing transaction
    // Runs WITHOUT a transaction
    // Useful for non-database operations
}
```

#### MANDATORY

```java
@Transactional(propagation = Propagation.MANDATORY)
public void criticalOperation() {
    // MUST have an existing transaction
    // If NO transaction → throws IllegalTransactionStateException
}
```

#### NEVER

```java
@Transactional(propagation = Propagation.NEVER)
public void nonTransactional() {
    // Must NOT have a transaction
    // If a transaction exists → throws IllegalTransactionStateException
}
```

#### NESTED

```java
@Transactional(propagation = Propagation.NESTED)
public void nestedOperation() {
    // Creates a SAVEPOINT within the existing transaction
    // Can rollback to savepoint without rolling back outer transaction
}
```

### Propagation Comparison

| Propagation | Existing TX? Use it? | No TX? Create one? | Use Case |
|-------------|---------------------|--------------------|---------| 
| REQUIRED (default) | Yes, join it | Yes, create new | Normal business methods |
| REQUIRES_NEW | No, suspend it | Yes, create new | Audit logs, independent operations |
| SUPPORTS | Yes, join it | No, run without | Read-only operations |
| NOT_SUPPORTED | No, suspend it | No, run without | Non-DB operations (email, SMS) |
| MANDATORY | Yes, join it | No, throw error | Must be called within TX |
| NEVER | No, throw error | No, run without | Must NOT be in TX |
| NESTED | Yes, create savepoint | Yes, create new | Partial rollback support |

### Practical Example

```java
@Service
public class OrderService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PaymentService paymentService;
    
    @Autowired
    private AuditService auditService;
    
    @Autowired
    private NotificationService notificationService;
    
    @Transactional  // Main transaction
    public void placeOrder(Order order) {
        
        // 1. Save order (REQUIRED — same transaction)
        orderRepository.save(order);
        
        // 2. Process payment (REQUIRED — same transaction)
        // If payment fails, order save is also rolled back ✅
        paymentService.processPayment(order);
        
        // 3. Log audit (REQUIRES_NEW — separate transaction)
        // Even if order fails later, audit log is saved ✅
        auditService.log("Order placed: " + order.getId());
        
        // 4. Send notification (NOT_SUPPORTED — no transaction needed)
        // Email sending doesn't need a database transaction
        notificationService.sendOrderConfirmation(order);
    }
}

@Service
public class PaymentService {
    @Transactional(propagation = Propagation.REQUIRED)  // Joins order transaction
    public void processPayment(Order order) {
        // If this fails → entire order + payment rolls back
    }
}

@Service
public class AuditService {
    @Transactional(propagation = Propagation.REQUIRES_NEW)  // Own transaction
    public void log(String message) {
        // If order fails → this audit log is STILL saved
    }
}

@Service
public class NotificationService {
    @Transactional(propagation = Propagation.NOT_SUPPORTED)  // No transaction
    public void sendOrderConfirmation(Order order) {
        // Sending emails — no database transaction needed
    }
}
```

---

## Quick Revision Cheat Sheet

```
JDBC Problems: Boilerplate, resource management, exception handling

JdbcTemplate:
  update(sql, args)          → INSERT, UPDATE, DELETE
  query(sql, rowMapper)      → SELECT multiple rows
  queryForObject(sql, rm, args) → SELECT single row
  
NamedParameterJdbcTemplate:
  Uses :name instead of ?
  More readable for complex queries
  Supports IN clause with lists

RowMapper:
  Converts ResultSet row → Java object
  mapRow(ResultSet rs, int rowNum) → Object
  BeanPropertyRowMapper = auto-mapping (column name → field name)

@Transactional:
  Groups operations → ALL succeed or ALL fail
  Works via proxy → only on public methods
  Default: rollback on RuntimeException only
  readOnly = true → optimization for read queries

Transaction Propagation:
  REQUIRED (default) → Join or create
  REQUIRES_NEW → Always create new (suspend existing)
  SUPPORTS → Join if exists, otherwise no TX
  NOT_SUPPORTED → Suspend TX, run without
  MANDATORY → Must be in TX, else error
  NEVER → Must NOT be in TX, else error
  NESTED → Savepoint within existing TX
```

---

**Next: [05-ORM-JPA-Hibernate.md](05-ORM-JPA-Hibernate.md) — ORM Concepts, JPA, Hibernate, and Entity Mappings**
