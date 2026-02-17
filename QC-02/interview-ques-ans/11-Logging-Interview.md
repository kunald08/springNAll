# Logging — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. Why do we use logging instead of System.out.println?

**Answer:**
`System.out.println` is fine for quick debugging, but in production it's completely inadequate:

| Feature | println | Logging Framework |
|---------|---------|-------------------|
| Log levels | No | Yes (TRACE, DEBUG, INFO, WARN, ERROR) |
| Output destination | Console only | Console, files, databases, remote servers |
| Performance | Always runs | Can disable levels in production |
| Formatting | Manual | Timestamps, thread names, class names automatically |
| Log rotation | No | Automatic file rotation by size/date |
| Thread info | No | Built-in thread name and context |

With logging, I can set the level to INFO in production (skipping DEBUG noise) and TRACE in development — without changing any code. `println` gives me none of that.

---

## 2. What are log levels? When do you use each?

**Answer:**
From most verbose to least:

| Level | When to Use | Example |
|-------|-------------|---------|
| **TRACE** | Super detailed, step-by-step flow | `Entering method calculate(5, 3)` |
| **DEBUG** | Developer-level info for debugging | `User query returned 42 rows` |
| **INFO** | Important business events | `Order #123 placed successfully` |
| **WARN** | Something unexpected but recoverable | `Cache miss — falling back to DB` |
| **ERROR** | Something failed, needs attention | `Payment gateway timeout for order #123` |
| **FATAL** | Application cannot continue | `Database connection pool exhausted — shutting down` |

In **production**, I typically set the level to INFO — so INFO, WARN, ERROR, and FATAL are logged, but DEBUG and TRACE are skipped. This keeps logs manageable.

---

## 3. What is SLF4J? Why is it called a facade?

**Answer:**
SLF4J (Simple Logging Facade for Java) is an **abstraction layer** — a facade — that sits between my application code and the actual logging library.

```
My Code → SLF4J API → Binding → Log4J 2 / Logback / JUL
```

I code against SLF4J's API:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

Logger logger = LoggerFactory.getLogger(MyClass.class);
logger.info("User {} logged in", username);
```

The benefit: if I want to switch from Logback to Log4J 2, I only change the **binding JAR** — zero changes to my code. The facade pattern decouples "what I log" from "how it's logged."

---

## 4. What is Log4J 2? Explain its architecture.

**Answer:**
Log4J 2 is a popular logging implementation. Its architecture has three main components:

1. **Logger** — the entry point in my code. Named hierarchically (e.g., `com.myapp.service`), inherits config from parent loggers.
2. **Appender** — the destination where logs go:
   - `ConsoleAppender` → stdout
   - `FileAppender` → single file
   - `RollingFileAppender` → rotates files by size/date
   - `AsyncAppender` → non-blocking, high performance
3. **Layout** — how each log line is formatted:
   - `PatternLayout` — customizable: `%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n`
   - `JSONLayout` — structured JSON for log aggregation tools

Log4J 2 also uses **async logging** by default with the LMAX Disruptor, making it significantly faster than Log4J 1.

---

## 5. How do you configure Log4J 2?

**Answer:**
Log4J 2 supports XML, JSON, YAML, and properties files. The most common is `log4j2.xml` on the classpath:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        <RollingFile name="File" fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="%d %-5level [%t] %logger - %msg%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="10 MB"/>
                <TimeBasedTriggeringPolicy interval="1"/>
            </Policies>
        </RollingFile>
    </Appenders>
    <Loggers>
        <Logger name="com.myapp" level="DEBUG"/>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

Loggers are hierarchical — `com.myapp.service` inherits from `com.myapp` which inherits from `Root`.

---

## 6. How do SLF4J and Log4J 2 work together?

**Answer:**
My code uses SLF4J's API. At runtime, SLF4J discovers the logging implementation through a **binding** JAR on the classpath.

Dependencies I need:
```xml
<!-- SLF4J API -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>
<!-- Log4J 2 as the implementation -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
</dependency>
```

Flow: `SLF4J API → log4j-slf4j2-impl (bridge) → Log4J 2 Core → Appenders`

If I later want Logback, I just swap the binding JAR. My code doesn't change.

---

## 7. What is parameterized logging?

**Answer:**
Instead of string concatenation, SLF4J uses `{}` placeholders:

```java
// BAD — always concatenates, even if DEBUG is disabled
logger.debug("User " + username + " has " + count + " orders");

// GOOD — placeholders only resolve if DEBUG is active
logger.debug("User {} has {} orders", username, count);
```

If the log level is set to INFO, the good version skips the string building entirely — saving CPU and memory. This is a **performance best practice** that interviewers expect you to know.

---

## 8. What are logging best practices?

**Answer:**
1. **Use SLF4J facade** — never tie code to a specific implementation
2. **Use parameterized logging** — `{}` placeholders, not concatenation
3. **Log at the right level** — don't log everything as INFO; use DEBUG for details
4. **Include context** — log user IDs, order numbers, request IDs for traceability
5. **Don't log sensitive data** — no passwords, tokens, or PII
6. **Use MDC** (Mapped Diagnostic Context) for request-scoped data like correlation IDs
7. **Configure rotation** — prevent log files from filling up disk
8. **Use async logging** in high-throughput applications
9. **Declare logger as:** `private static final Logger logger = LoggerFactory.getLogger(MyClass.class);`
10. **Guard expensive logging:** `if (logger.isDebugEnabled()) { logger.debug(...); }` — only needed when computing log arguments is expensive

---

## 9. What is MDC (Mapped Diagnostic Context)?

**Answer:**
MDC lets me attach **request-scoped data** to every log line automatically:

```java
MDC.put("requestId", UUID.randomUUID().toString());
MDC.put("userId", currentUser.getId());

// In log4j2.xml pattern: %X{requestId} %X{userId}
// Output: [req-abc123] [user-42] Processing order...

MDC.clear(); // Clean up after request
```

This is essential in web applications — when multiple requests run in parallel, MDC tags every log line with the right request ID so I can trace a single request's journey through the entire system.

---

## 10. What is log rotation? Why is it important?

**Answer:**
Log rotation automatically archives old log files and creates new ones based on **size** or **time** to prevent disk space exhaustion.

```xml
<RollingFile name="File" fileName="logs/app.log"
             filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
    <Policies>
        <SizeBasedTriggeringPolicy size="10 MB"/>
        <TimeBasedTriggeringPolicy interval="1"/>
    </Policies>
    <DefaultRolloverStrategy max="30"/>
</RollingFile>
```

This config:
- Rotates when file reaches 10 MB
- Also rotates daily
- Compresses old files to `.gz`
- Keeps max 30 archived files

Without rotation, a production app can fill up disk in hours, potentially crashing the server.

---

*Back to: [11-Logging.md](../11-Logging.md)*
