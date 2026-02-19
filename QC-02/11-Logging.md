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

## MDC — Mapped Diagnostic Context (Adding Context to Every Log Line)

### The Problem

In a web application, multiple users make requests at the same time. Your logs look like this:

```
2024-01-15 10:30:01 INFO  Processing order
2024-01-15 10:30:01 INFO  Processing order
2024-01-15 10:30:01 INFO  Checking inventory
2024-01-15 10:30:01 INFO  Payment successful
2024-01-15 10:30:02 INFO  Checking inventory
2024-01-15 10:30:02 INFO  Payment failed
```

Which log lines belong to which user? Which request? **You can't tell!** This is a nightmare when debugging production issues.

### The Solution: MDC

**MDC (Mapped Diagnostic Context)** lets you attach contextual data (like user ID, request ID, session ID) to every log line automatically. It's like a sticky note that follows every log message around.

```java
import org.slf4j.MDC;

// In a web filter (runs before every request):
public class RequestFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                         FilterChain chain) throws IOException, ServletException {
        try {
            // Generate a unique ID for this request
            String requestId = UUID.randomUUID().toString().substring(0, 8);
            String userId = ((HttpServletRequest) request).getHeader("X-User-Id");
            
            // Put context into MDC — these values will appear in ALL log lines
            // for this thread, automatically!
            MDC.put("requestId", requestId);
            MDC.put("userId", userId != null ? userId : "anonymous");
            
            // Continue processing the request
            chain.doFilter(request, response);
            
        } finally {
            MDC.clear();  // ALWAYS clean up! Otherwise values leak to other requests.
        }
    }
}
```

```xml
<!-- In your log4j2.xml or logback.xml, use %X{key} to print MDC values: -->
<PatternLayout pattern="%d [%X{requestId}] [%X{userId}] %-5p %c - %m%n"/>
```

Now your logs look like this — **every line has context!**

```
2024-01-15 10:30:01 [a1b2c3d4] [kunal]  INFO  Processing order
2024-01-15 10:30:01 [e5f6g7h8] [priya]  INFO  Processing order
2024-01-15 10:30:01 [a1b2c3d4] [kunal]  INFO  Checking inventory
2024-01-15 10:30:01 [a1b2c3d4] [kunal]  INFO  Payment successful
2024-01-15 10:30:02 [e5f6g7h8] [priya]  INFO  Checking inventory
2024-01-15 10:30:02 [e5f6g7h8] [priya]  ERROR Payment failed

Now you can easily filter: "Show me all logs for request a1b2c3d4"
→ Instantly see the full journey of Kunal's request!
```

---

## Logback — The Default Logger in Spring Boot

### Why Logback?

If you use **Spring Boot**, you're already using Logback! It's included automatically. You don't need to add any dependencies. Logback was created by the same person who created SLF4J and Log4j — so it's well-designed and fast.

```
Log4J 2  → Standalone, very fast, used outside Spring
Logback  → Created by SLF4J author, Spring Boot's default, simpler config
Both     → Work with SLF4J (same API in your code!)
```

### Logback Configuration (logback-spring.xml)

Spring Boot looks for `logback-spring.xml` or `logback.xml` in the `src/main/resources` folder:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    
    <!-- Console output (colored!) -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %highlight(%-5level) %cyan(%logger{36}) - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- File output with daily rotation -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>  <!-- Keep 30 days of logs -->
            <totalSizeCap>1GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] [%X{requestId}] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- Set levels for specific packages -->
    <logger name="com.myapp" level="DEBUG"/>           <!-- Your app: DEBUG level -->
    <logger name="org.springframework" level="WARN"/>  <!-- Spring: only WARN+ -->
    <logger name="org.hibernate.SQL" level="DEBUG"/>   <!-- See SQL queries -->
    
    <!-- Root logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
    </root>
    
</configuration>
```

Or even simpler — just use `application.properties` (Spring Boot feature):

```properties
# In application.properties (no XML needed for basic config!):
logging.level.root=INFO
logging.level.com.myapp=DEBUG
logging.level.org.hibernate.SQL=DEBUG
logging.file.name=logs/app.log
logging.pattern.console=%d{HH:mm:ss} %-5level %logger{36} - %msg%n
```

---

## Structured Logging — JSON Logs for Production

### Why Structured Logging?

Plain text logs are fine for reading in your terminal, but in production, logs are sent to log management tools like **ELK Stack (Elasticsearch + Logstash + Kibana)**, **Splunk**, or **Datadog**. These tools work MUCH better with **JSON formatted logs** because they can search, filter, and analyze structured data.

```
Plain text log (hard for machines to parse):
  2024-01-15 10:30:01 INFO [a1b2c3d4] OrderService - Order placed for user 42, total $99.99

JSON structured log (easy for machines to parse):
  {
    "timestamp": "2024-01-15T10:30:01.000Z",
    "level": "INFO",
    "requestId": "a1b2c3d4",
    "logger": "OrderService",
    "message": "Order placed",
    "userId": 42,
    "orderTotal": 99.99
  }

Now Elasticsearch can: "Find all orders where orderTotal > 50 AND level = ERROR"
→ Impossible to do reliably with plain text!
```

### Setting Up JSON Logging with Logback

```xml
<!-- Add this dependency for JSON encoder -->
<!-- <groupId>net.logstash.logback</groupId> -->
<!-- <artifactId>logstash-logback-encoder</artifactId> -->

<!-- In logback-spring.xml: -->
<appender name="JSON_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logs/app.json</file>
    <encoder class="net.logstash.logback.encoder.LogstashEncoder">
        <!-- Automatically includes timestamp, level, logger, message, MDC, stack traces -->
    </encoder>
</appender>
```

---

## Async Logging — Don't Let Logging Slow Down Your App

### The Problem

Writing logs to disk takes time (disk I/O). If your app logs thousands of messages per second, the logging itself can slow down your application — the thread has to WAIT for the log to be written to disk before continuing.

### The Solution: Async Logging

With async logging, log messages are put into a **queue** in memory (very fast — microseconds), and a separate background thread writes them to disk. Your application thread doesn't wait!

```
Synchronous logging (slow):
  Your code → log.info("msg") → Write to disk (5ms) → Continue code
  ↑ Your thread is BLOCKED for 5ms

Asynchronous logging (fast):
  Your code → log.info("msg") → Put in queue (0.001ms) → Continue code immediately!
                                    ↓
                              Background thread → Write to disk (5ms)
  ↑ Your thread is NOT blocked!
```

### Log4J 2 Async Logging

```xml
<!-- Log4J 2: AsyncAppender wraps a regular appender -->
<Configuration>
    <Appenders>
        <File name="SyncFile" fileName="logs/app.log">
            <PatternLayout pattern="%d %-5p %c - %m%n"/>
        </File>
        
        <Async name="AsyncFile">
            <AppenderRef ref="SyncFile"/>
            <bufferSize>1024</bufferSize>  <!-- Queue size -->
        </Async>
    </Appenders>
    
    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="AsyncFile"/>  <!-- Use the async version -->
        </Root>
    </Loggers>
</Configuration>
```

### Logback Async Logging

```xml
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>logs/app.log</file>
        <encoder>
            <pattern>%d %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="FILE"/>
        <queueSize>1024</queueSize>
        <discardingThreshold>0</discardingThreshold>  <!-- Don't discard any logs -->
    </appender>

    <root level="INFO">
        <appender-ref ref="ASYNC"/>
    </root>
</configuration>
```

```
⚠️ Warning about Async Logging:
  - If the app crashes, some log messages in the queue might be LOST
    (they haven't been written to disk yet)
  - For CRITICAL logs (like financial transactions), use synchronous logging
  - For INFO/DEBUG logs, async is fine and much faster
```

---

*Previous: [10-Mockito-Mocking.md](10-Mockito-Mocking.md)*
*Next: [12-Code-Quality-Analysis.md](12-Code-Quality-Analysis.md)*
