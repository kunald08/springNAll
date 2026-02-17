# Logging — Log4J 2 & SLF4J

## Table of Contents
1. [Why Logging?](#1-why-logging)
2. [Log Levels](#2-log-levels)
3. [SLF4J — The Logging Facade](#3-slf4j--the-logging-facade)
4. [Log4J 2 — The Logging Implementation](#4-log4j-2--the-logging-implementation)
5. [Appenders — Where Logs Go](#5-appenders--where-logs-go)
6. [Layouts — How Logs Look](#6-layouts--how-logs-look)
7. [Configuration](#7-configuration)
8. [Best Practices](#8-best-practices)
9. [SLF4J + Log4J 2 Binding](#9-slf4j--log4j-2-binding)

---

## 1. Why Logging?

```
System.out.println("Debug: user logged in");   // ❌ BAD 

Why not System.out.println?
1. Can't turn off without deleting code
2. Can't write to files
3. No timestamps
4. No log levels (debug vs error)
5. No formatting control
6. Hurts performance in production
7. Can't configure different output per package

Logging framework:
logger.info("User {} logged in", username);     // ✅ GOOD

Benefits:
1. Turn on/off via configuration (no code changes!)
2. Write to files, databases, email, remote servers
3. Automatic timestamps and metadata
4. Filter by level (show only errors in production)
5. Rich formatting with patterns
6. Zero-cost when disabled (lazy evaluation)
7. Different config per package/class
```

---

## 2. Log Levels

```
Levels from LEAST to MOST severe:

┌──────────┬──────────────────────────────────────────────────┐
│  TRACE   │ Very detailed — method entry/exit, loop values   │
│          │ "Entering method calculateTax with salary=50000" │
├──────────┼──────────────────────────────────────────────────┤
│  DEBUG   │ Debugging info — variable values, flow decisions │
│          │ "User found in database: id=42, name=Alice"      │
├──────────┼──────────────────────────────────────────────────┤
│  INFO    │ General milestones — app started, user actions   │
│          │ "Application started on port 8080"               │
├──────────┼──────────────────────────────────────────────────┤
│  WARN    │ Potential problems — deprecated API, retry       │
│          │ "Database connection slow, retrying..."          │
├──────────┼──────────────────────────────────────────────────┤
│  ERROR   │ Errors that are handled — catch blocks           │
│          │ "Failed to send email: connection refused"       │
├──────────┼──────────────────────────────────────────────────┤
│  FATAL   │ App cannot continue — out of memory, DB down     │
│          │ "Cannot connect to database. Shutting down."     │
└──────────┴──────────────────────────────────────────────────┘

When you set a level, you see that level AND everything above it:
- Set to DEBUG → see DEBUG, INFO, WARN, ERROR, FATAL
- Set to WARN  → see WARN, ERROR, FATAL (no DEBUG or INFO)
- Set to ERROR → see ERROR, FATAL only

Typical settings:
- Development: DEBUG or TRACE (see everything)
- Production:  INFO or WARN (see important stuff only)
```

---

## 3. SLF4J — The Logging Facade

**SLF4J** (Simple Logging Facade for Java) is an **abstraction layer**. It defines a common API — you code against SLF4J, and swap the actual logging library without changing code.

```
Your Code → SLF4J API → Binding → Log4J 2 / Logback / java.util.logging

┌──────────────┐      ┌──────────┐      ┌────────────────┐
│  Your Code   │────▶│  SLF4J   │────▶│  Log4J 2       │  (or Logback, etc.)
│  import org. │      │  API     │      │  Implementation│
│  slf4j.*     │      │ (facade) │      │                │
└──────────────┘      └──────────┘      └────────────────┘

Why use SLF4J instead of Log4J directly?
1. FLEXIBILITY — switch from Log4J to Logback without changing code
2. LIBRARY COMPATIBILITY — different libraries may use different loggers
3. STANDARDIZATION — one API for all logging needs
```

### Using SLF4J

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {
    // Create a logger for this class
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);
    
    public User getUser(int id) {
        logger.debug("Looking up user with id: {}", id);   // {} is a placeholder
        
        User user = repository.findById(id);
        
        if (user == null) {
            logger.warn("User not found: id={}", id);
            throw new UserNotFoundException("User not found: " + id);
        }
        
        logger.info("User found: {} ({})", user.getName(), user.getEmail());
        return user;
    }
    
    public void deleteUser(int id) {
        try {
            repository.delete(id);
            logger.info("User deleted: id={}", id);
        } catch (Exception e) {
            logger.error("Failed to delete user: id={}", id, e);  // 'e' logs stack trace!
        }
    }
}
```

### Parameterized Logging (Why {} Is Better)

```java
// ❌ BAD — string concatenation happens EVEN IF logging is disabled!
logger.debug("User: " + user.getName() + " age: " + user.getAge());
// If debug is disabled, the string is still built (wasted CPU)

// ✅ GOOD — placeholders are only resolved if the level is enabled
logger.debug("User: {} age: {}", user.getName(), user.getAge());
// If debug is disabled, getName() and getAge() are never called!

// For very expensive operations, check the level first:
if (logger.isDebugEnabled()) {
    logger.debug("Full user details: {}", user.toDetailedString());
}
```

---

## 4. Log4J 2 — The Logging Implementation

**Log4J 2** is the actual logging engine. It does the real work: formatting, filtering, writing to files, etc.

### Architecture

```
┌────────────────────────────────────────────────────┐
│                   Log4J 2                          │
│                                                    │
│  Logger ──▶ LoggerConfig ──▶ Appender ──▶ Layout│
│                                                    │
│  "Who      "What level    "Where to    "How to     │
│   logs?"    to log?"       write?"      format?"   │
└────────────────────────────────────────────────────┘

Logger:       Named logger (usually class name)
LoggerConfig: Level filter + which appenders to use
Appender:     Output destination (console, file, database)
Layout:       Format of the log message
```

### Maven Dependencies

```xml
<!-- Log4J 2 Core -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.23.1</version>
</dependency>

<!-- Log4J 2 API -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.23.1</version>
</dependency>

<!-- SLF4J to Log4J 2 bridge (if using SLF4J API) -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.23.1</version>
</dependency>
```

---

## 5. Appenders — Where Logs Go

```
Appender Types:
┌──────────────────────┬──────────────────────────────────────┐
│ ConsoleAppender      │ Writes to System.out or System.err   │
│ FileAppender         │ Writes to a file                     │
│ RollingFileAppender  │ Writes to files, rotates when full   │
│ JDBCAppender         │ Writes to a database table           │
│ SMTPAppender         │ Sends email on certain log events    │
│ SocketAppender       │ Sends logs over network              │
│ AsyncAppender        │ Wraps another appender for async I/O │
└──────────────────────┴──────────────────────────────────────┘
```

### Console Appender

```xml
<Console name="Console" target="SYSTEM_OUT">
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
</Console>
```

### File Appender

```xml
<File name="File" fileName="logs/app.log">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"/>
</File>
```

### Rolling File Appender (Most Common in Production)

```xml
<!-- Creates new log file when size exceeds 10MB or daily -->
<RollingFile name="RollingFile" fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
    <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"/>
    <Policies>
        <SizeBasedTriggeringPolicy size="10 MB"/>     <!-- New file every 10MB -->
        <TimeBasedTriggeringPolicy interval="1"/>     <!-- New file every day -->
    </Policies>
    <DefaultRolloverStrategy max="30"/>               <!-- Keep max 30 files -->
</RollingFile>
```

---

## 6. Layouts — How Logs Look

### Pattern Layout

```
Common pattern symbols:
%d      — date/time                     2025-03-15 14:30:45.123
%p      — log level                     INFO, ERROR, DEBUG
%-5p    — log level, left-padded to 5   "INFO ", "ERROR"
%c      — logger name (full)            com.example.UserService
%c{1}   — logger name (short)           UserService
%logger — same as %c
%t      — thread name                   main, Thread-1
%m      — the log message               "User logged in"
%msg    — same as %m
%n      — newline
%ex     — exception stack trace
%L      — line number (slow!)
%M      — method name (slow!)

Example patterns:
Simple:   %d %-5p %c{1} - %m%n
          2025-03-15 14:30:45 INFO  UserService - User logged in

Detailed: %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n
          2025-03-15 14:30:45.123 [main] INFO  com.example.UserService - User logged in
```

### JSON Layout (for log aggregation tools like ELK)

```xml
<JsonLayout compact="true" eventEol="true"/>
<!-- Output: {"timestamp":"2025-03-15T14:30:45","level":"INFO","logger":"UserService","message":"User logged in"} -->
```

---

## 7. Configuration

### log4j2.xml (Most Common)

Place in `src/main/resources/log4j2.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">

    <!-- Define appenders -->
    <Appenders>
        <!-- Console output -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        
        <!-- File output with rotation -->
        <RollingFile name="File" fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="10 MB"/>
                <TimeBasedTriggeringPolicy interval="1"/>
            </Policies>
            <DefaultRolloverStrategy max="30"/>
        </RollingFile>
    </Appenders>

    <!-- Define loggers -->
    <Loggers>
        <!-- Root logger — catches everything not matched by specific loggers -->
        <Root level="info">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Root>
        
        <!-- Package-specific logger (more verbose for your code) -->
        <Logger name="com.example" level="debug" additivity="false">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Logger>
        
        <!-- Quiet down noisy libraries -->
        <Logger name="org.hibernate" level="warn"/>
        <Logger name="org.springframework" level="info"/>
    </Loggers>

</Configuration>
```

### Logger Hierarchy

```
Loggers are hierarchical based on package names:

Root Logger (catches everything)
├── com
│   └── example
│       ├── service       → uses com.example config (level=debug)
│       │   └── UserService
│       └── repository    → uses com.example config (level=debug)
├── org
│   └── hibernate         → uses org.hibernate config (level=warn)
└── (everything else)     → uses Root config (level=info)

Additivity:
- By default, a logger sends events to ITS appenders AND parent's appenders
- additivity="false" prevents this duplication
```

---

## 8. Best Practices

```java
// 1. Use SLF4J API, not Log4J directly
import org.slf4j.Logger;                         // ✅ SLF4J
// import org.apache.logging.log4j.Logger;        // ❌ Direct Log4J

// 2. Logger should be private static final
private static final Logger logger = LoggerFactory.getLogger(MyClass.class);

// 3. Use parameterized messages (NOT string concatenation)
logger.info("User {} logged in from {}", username, ip);     // ✅
// logger.info("User " + username + " logged in from " + ip);  // ❌

// 4. Log exceptions properly — pass exception as LAST argument
try {
    // ...
} catch (Exception e) {
    logger.error("Failed to process order: orderId={}", orderId, e);  // ✅ logs stack trace
    // logger.error("Failed: " + e.getMessage());                      // ❌ loses stack trace
}

// 5. Use appropriate log levels
logger.trace("Entering method with param: {}", param);    // Method-level tracing
logger.debug("Query returned {} results", count);          // Debugging details
logger.info("Order {} created successfully", orderId);     // Business events
logger.warn("Cache miss for key: {}", key);                // Potential issues
logger.error("Payment failed for order: {}", orderId, ex); // Errors

// 6. Don't log sensitive information
// logger.info("User login: password={}", password);   // ❌ NEVER!
logger.info("User login: username={}", username);       // ✅

// 7. Don't log in tight loops (performance!)
for (int i = 0; i < 1_000_000; i++) {
    // logger.debug("Processing item {}", i);   // ❌ Million log entries!
    items.add(process(i));
}
logger.info("Processed {} items", items.size());   // ✅ One summary log

// 8. Include context in log messages
logger.info("Order processed: orderId={}, userId={}, total={}", orderId, userId, total);
// Not just: logger.info("Order processed");
```

---

## 9. SLF4J + Log4J 2 Binding

```
How SLF4J finds Log4J 2:

Your Code
   │
   │  import org.slf4j.Logger
   ▼
┌───────────┐
│ SLF4J API │  slf4j-api.jar
└─────┬─────┘
      │  SLF4J looks for a "binding" on the classpath
      ▼
┌──────────────────┐
│ log4j-slf4j2-impl│  This JAR bridges SLF4J → Log4J 2
└─────┬────────────┘
      ▼
┌───────────┐
│ Log4J 2   │  log4j-core.jar + log4j-api.jar
│ Engine    │  Does the actual logging
└───────────┘

Maven dependencies you need:
1. slf4j-api           — SLF4J interface
2. log4j-slf4j2-impl   — Bridge from SLF4J to Log4J 2
3. log4j-core          — Log4J 2 engine
4. log4j-api           — Log4J 2 API

If you see "SLF4J: No SLF4J providers were found"
→ You're missing the binding JAR (log4j-slf4j2-impl)
```

### Complete Maven Setup

```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.12</version>
</dependency>

<!-- SLF4J → Log4J 2 Bridge -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.23.1</version>
</dependency>

<!-- Log4J 2 Core -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.23.1</version>
</dependency>

<!-- Log4J 2 API -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.23.1</version>
</dependency>
```

---

*Previous: [10-Mockito-Mocking.md](10-Mockito-Mocking.md)*
*Next: [12-Code-Quality-Analysis.md](12-Code-Quality-Analysis.md)*
