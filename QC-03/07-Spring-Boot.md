# Spring Boot - Complete In-Depth Guide

## Table of Contents
- [1. What is Spring Boot?](#1-what-is-spring-boot)
- [2. Spring Initializr](#2-spring-initializr)
- [3. Project Structure](#3-project-structure)
- [4. Auto-Configuration](#4-auto-configuration)
- [5. Starter Dependencies](#5-starter-dependencies)
- [6. @SpringBootApplication](#6-springbootapplication)
- [7. Application Properties](#7-application-properties)
- [8. Profile Management](#8-profile-management)

---

## 1. What is Spring Boot?

### Simple Explanation

Spring Boot is a project built **on top of Spring Framework** that makes it super easy to create Spring applications with **minimal configuration**.

### The Problem Spring Boot Solves

Setting up a traditional Spring application was painful:

```
Without Spring Boot:
1. Create project structure manually
2. Add 20+ dependencies with correct versions
3. Write XML configuration files (hundreds of lines)
4. Configure web server (Tomcat/Jetty)
5. Configure database connection
6. Configure view resolver
7. Configure dispatcher servlet
8. Configure component scanning
9. ... and much more BEFORE you even write business code!

With Spring Boot:
1. Go to start.spring.io
2. Select dependencies
3. Download → Run → DONE! 🎉
```

### Spring vs Spring Boot

| Feature | Spring Framework | Spring Boot |
|---------|-----------------|-------------|
| Configuration | Manual (XML or Java) | Auto-configured |
| Server | External (install Tomcat) | Embedded (comes built-in) |
| Dependencies | Find compatible versions yourself | Starter POMs handle it |
| Setup time | Hours | Minutes |
| Boilerplate | Lots | Almost none |
| Production-ready | Manual setup | Built-in (actuator, health) |

### Key Principles

1. **Convention over Configuration** — Sensible defaults, override only when needed
2. **Opinionated** — Makes decisions for you (you can override)
3. **Standalone** — Run as a simple JAR (no external server needed)
4. **Production-ready** — Health checks, metrics, externalized config

---

## 2. Spring Initializr

### What is Spring Initializr?

**Spring Initializr** (https://start.spring.io) is a web-based tool that generates a Spring Boot project skeleton for you.

### How to Use It

```
1. Go to https://start.spring.io
2. Choose:
   - Project: Maven (or Gradle)
   - Language: Java
   - Spring Boot version: 3.2.x (latest stable)
   - Group: com.example
   - Artifact: my-app
   - Packaging: Jar
   - Java: 17
3. Add Dependencies:
   - Spring Web (for REST APIs)
   - Spring Data JPA (for database)
   - MySQL Driver (or H2 for testing)
   - Spring Boot DevTools (auto-restart)
4. Click "Generate" → Downloads a ZIP file
5. Extract and open in your IDE
```

### What It Generates

```
my-app/
├── pom.xml                      ← Pre-configured with correct dependencies
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/myapp/
│   │   │       └── MyAppApplication.java  ← Main class
│   │   └── resources/
│   │       ├── application.properties     ← Configuration
│   │       ├── static/                    ← CSS, JS, images
│   │       └── templates/                 ← HTML templates
│   └── test/
│       └── java/
│           └── com/example/myapp/
│               └── MyAppApplicationTests.java  ← Test class
├── mvnw                         ← Maven wrapper (Linux/Mac)
├── mvnw.cmd                     ← Maven wrapper (Windows)
└── .mvn/                        ← Maven wrapper config
```

---

## 3. Project Structure

### Standard Spring Boot Layout

```
com.example.myapp/
├── MyAppApplication.java        ← Main class (@SpringBootApplication)
├── controller/                  ← REST controllers / web controllers
│   ├── UserController.java
│   └── OrderController.java
├── service/                     ← Business logic
│   ├── UserService.java
│   └── OrderService.java
├── repository/                  ← Database access
│   ├── UserRepository.java
│   └── OrderRepository.java
├── model/                       ← Entity classes / domain objects
│   ├── User.java
│   └── Order.java
├── dto/                         ← Data Transfer Objects
│   ├── UserDTO.java
│   └── OrderDTO.java
├── config/                      ← Configuration classes
│   └── AppConfig.java
├── exception/                   ← Custom exceptions
│   └── ResourceNotFoundException.java
└── util/                        ← Utility/helper classes
    └── DateUtil.java
```

### Layered Architecture

```
┌────────────────────────────────────────┐
│           Controller Layer              │  ← Handles HTTP requests
│  @RestController / @Controller          │
├────────────────────────────────────────┤
│            Service Layer                │  ← Business logic
│  @Service                               │
├────────────────────────────────────────┤
│          Repository Layer               │  ← Database operations
│  @Repository / JpaRepository            │
├────────────────────────────────────────┤
│          Database                       │  ← MySQL, PostgreSQL, etc.
└────────────────────────────────────────┘

HTTP Request → Controller → Service → Repository → Database
HTTP Response ← Controller ← Service ← Repository ← Database
```

---

## 4. Auto-Configuration

### What is Auto-Configuration?

Spring Boot **automatically configures** your application based on the dependencies in your classpath. You don't need to write configuration code.

### How It Works

```
You add spring-boot-starter-data-jpa + MySQL driver to pom.xml

Spring Boot automatically:
✅ Creates a DataSource (database connection pool)
✅ Configures Hibernate as JPA provider
✅ Sets up EntityManagerFactory
✅ Configures TransactionManager
✅ Enables JPA repositories

You add spring-boot-starter-web to pom.xml

Spring Boot automatically:
✅ Starts embedded Tomcat on port 8080
✅ Configures DispatcherServlet
✅ Sets up JSON serialization (Jackson)
✅ Configures error handling

ALL OF THIS WITHOUT YOU WRITING A SINGLE LINE OF CONFIG!
```

### Under the Hood: How Auto-Configuration Works

```
Step 1: @SpringBootApplication includes @EnableAutoConfiguration

Step 2: Spring Boot reads META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
        (This file lists ALL auto-configuration classes)

Step 3: Each class has CONDITIONS:
        @ConditionalOnClass → "Only if this class is on classpath"
        @ConditionalOnMissingBean → "Only if user hasn't defined their own"
        @ConditionalOnProperty → "Only if this property is set"

Step 4: If conditions are met → configuration is applied

Example: DataSourceAutoConfiguration
  @ConditionalOnClass(DataSource.class)           → Do you have JDBC?
  @ConditionalOnMissingBean(DataSource.class)      → Did you NOT define your own?
  → If YES to both: Create a HikariCP DataSource for you
```

### Real Example of Auto-Configuration Source Code

```java
// This is what Spring Boot does behind the scenes:
@Configuration
@ConditionalOnClass(DataSource.class)      // Only if DataSource class exists
@ConditionalOnMissingBean(DataSource.class) // Only if user didn't create one
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public DataSource dataSource(DataSourceProperties properties) {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl(properties.getUrl());
        ds.setUsername(properties.getUsername());
        ds.setPassword(properties.getPassword());
        return ds;
    }
}

// But if YOU define your own DataSource:
@Configuration
public class MyConfig {
    @Bean
    public DataSource dataSource() {
        // Your custom DataSource — Spring Boot WON'T auto-configure one
        return new CustomDataSource();
    }
}
```

### Disabling Auto-Configuration

```java
// Exclude specific auto-configurations
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,
    SecurityAutoConfiguration.class
})
public class MyApp { }

// Or in application.properties:
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

---

## 5. Starter Dependencies

### What are Starters?

Starters are **pre-packaged sets of dependencies** that work together. Instead of adding 10 individual dependencies, you add 1 starter.

### Common Starters

| Starter | What It Includes | Use Case |
|---------|-----------------|----------|
| `spring-boot-starter-web` | Spring MVC, Tomcat, Jackson | REST APIs, Web apps |
| `spring-boot-starter-data-jpa` | Spring Data JPA, Hibernate | Database with ORM |
| `spring-boot-starter-jdbc` | Spring JDBC, HikariCP | Database with SQL |
| `spring-boot-starter-security` | Spring Security | Authentication/Authorization |
| `spring-boot-starter-test` | JUnit, Mockito, AssertJ | Testing |
| `spring-boot-starter-validation` | Hibernate Validator | Input validation |
| `spring-boot-starter-thymeleaf` | Thymeleaf template engine | Server-side HTML |
| `spring-boot-starter-actuator` | Health, metrics, monitoring | Production monitoring |
| `spring-boot-starter-mail` | JavaMail | Sending emails |

### What's Inside a Starter?

```xml
<!-- YOU write: -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- What it ACTUALLY includes (pulled automatically): -->
<!-- spring-boot-starter (core) -->
<!-- spring-web -->
<!-- spring-webmvc -->
<!-- spring-boot-starter-tomcat -->
<!--   └── tomcat-embed-core -->
<!--   └── tomcat-embed-el -->
<!--   └── tomcat-embed-websocket -->
<!-- spring-boot-starter-json -->
<!--   └── jackson-databind -->
<!--   └── jackson-datatype-jdk8 -->
<!--   └── jackson-datatype-jsr310 -->
<!--   └── jackson-module-parameter-names -->

<!-- ONE dependency → 15+ libraries automatically! -->
```

---

## 6. @SpringBootApplication

### What is @SpringBootApplication?

It's a **combination of three annotations** in one:

```java
@SpringBootApplication
public class MyAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyAppApplication.class, args);
    }
}
```

### Under the Hood: What It Combines

```java
@SpringBootApplication  =  @Configuration
                          + @EnableAutoConfiguration
                          + @ComponentScan

// It's equivalent to:
@Configuration           // This class can define @Bean methods
@EnableAutoConfiguration // Enable Spring Boot auto-configuration
@ComponentScan           // Scan this package + sub-packages for components
public class MyAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyAppApplication.class, args);
    }
}
```

### What SpringApplication.run() Does

```
SpringApplication.run(MyAppApplication.class, args);

Step 1: Create ApplicationContext (Spring Container)
Step 2: Register configuration classes
Step 3: Perform component scanning (find @Component, @Service, etc.)
Step 4: Run auto-configuration (based on classpath)
Step 5: Create and wire all beans
Step 6: Start embedded web server (Tomcat)
Step 7: Application is READY! 🚀

Console output:
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.2.0)

Started MyAppApplication in 2.345 seconds (process running for 3.456)
```

### Important: Main Class Location

```
com.example.myapp/
├── MyAppApplication.java    ← @SpringBootApplication HERE
├── controller/              ← ✅ SCANNED (sub-package)
├── service/                 ← ✅ SCANNED (sub-package)
└── repository/              ← ✅ SCANNED (sub-package)

com.example.other/           ← ❌ NOT SCANNED (different package!)
```

The main class should be in the **root package** so that all sub-packages are scanned.

---

## 7. Application Properties

### What is application.properties?

The central configuration file for Spring Boot. Customizes everything from server port to database URL.

### Key Properties by Category

```properties
# ========== SERVER ==========
server.port=8080                          # Server port
server.servlet.context-path=/api          # Base URL path
server.error.include-message=always       # Show error messages

# ========== DATABASE ==========
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Connection pool (HikariCP)
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000

# ========== JPA / HIBERNATE ==========
spring.jpa.hibernate.ddl-auto=update      # Auto-create/update tables
spring.jpa.show-sql=true                  # Print SQL to console
spring.jpa.properties.hibernate.format_sql=true  # Pretty-print SQL
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect

# ddl-auto options:
# none     → Do nothing
# validate → Validate schema, don't change
# update   → Update schema (ADD columns, won't DELETE)
# create   → Drop and create tables on startup
# create-drop → create + drop on shutdown

# ========== LOGGING ==========
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.level.org.hibernate.SQL=DEBUG
logging.file.name=app.log
logging.pattern.console=%d{HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# ========== CUSTOM PROPERTIES ==========
app.name=My Application
app.version=2.0
app.admin-email=admin@example.com
app.max-upload-size=10MB
```

### YAML Format (application.yml)

```yaml
server:
  port: 8080
  servlet:
    context-path: /api

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

app:
  name: My Application
  version: 2.0
```

### Binding Properties to a Java Class

```java
// application.properties:
// app.name=My Application
// app.version=2.0
// app.admin-email=admin@example.com
// app.features.feature1=true
// app.features.feature2=false

@Component
@ConfigurationProperties(prefix = "app")  // Binds all "app.*" properties
public class AppProperties {
    
    private String name;
    private String version;
    private String adminEmail;
    private Map<String, Boolean> features;
    
    // Getters and setters are REQUIRED for binding
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    // ... more getters/setters
}

// Enable configuration properties binding:
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class MyApp { }

// Use it anywhere:
@Service
public class MyService {
    
    @Autowired
    private AppProperties appProperties;
    
    public void showConfig() {
        System.out.println("App: " + appProperties.getName());
        System.out.println("Version: " + appProperties.getVersion());
    }
}
```

---

## 8. Profile Management

### What are Profiles?

Profiles let you have **different configurations for different environments** (development, testing, production). You switch between them easily.

### Why Profiles?

```
Development:  H2 in-memory database, debug logging, port 8080
Testing:      Test database, warn logging
Production:   MySQL production server, info logging, port 80, HTTPS

Instead of changing properties files every time you deploy,
use PROFILES to switch automatically!
```

### Setting Up Profiles

#### Profile-Specific Properties Files

```
src/main/resources/
├── application.properties              ← Common (default)
├── application-dev.properties           ← Development
├── application-test.properties          ← Testing
└── application-prod.properties          ← Production
```

```properties
# application.properties (COMMON — applies to all profiles)
app.name=My Application
spring.profiles.active=dev   # Active profile (change this to switch)

# application-dev.properties (DEVELOPMENT)
server.port=8080
spring.datasource.url=jdbc:h2:mem:devdb
spring.datasource.driver-class-name=org.h2.Driver
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
logging.level.com.example=DEBUG

# application-test.properties (TESTING)
server.port=8081
spring.datasource.url=jdbc:h2:mem:testdb
spring.jpa.hibernate.ddl-auto=create
logging.level.com.example=WARN

# application-prod.properties (PRODUCTION)
server.port=80
spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
spring.datasource.username=${DB_USERNAME}   # From environment variable
spring.datasource.password=${DB_PASSWORD}   # From environment variable
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
logging.level.com.example=INFO
```

### Activating Profiles

```properties
# Way 1: In application.properties
spring.profiles.active=dev

# Way 2: Command line argument
java -jar myapp.jar --spring.profiles.active=prod

# Way 3: Environment variable
export SPRING_PROFILES_ACTIVE=prod

# Way 4: JVM system property
java -Dspring.profiles.active=prod -jar myapp.jar

# Way 5: Multiple profiles
spring.profiles.active=prod,logging,metrics
```

### Profile-Specific Beans

```java
@Configuration
public class DataSourceConfig {
    
    @Bean
    @Profile("dev")  // Only created when "dev" profile is active
    public DataSource devDataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }
    
    @Bean
    @Profile("prod")  // Only created when "prod" profile is active
    public DataSource prodDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://prod-server:3306/proddb");
        return ds;
    }
}

// Profile-specific component
@Service
@Profile("dev")
public class MockPaymentService implements PaymentService {
    // Fake payment for development
    public void charge(double amount) {
        System.out.println("MOCK: Charged $" + amount);
    }
}

@Service
@Profile("prod")
public class RealPaymentService implements PaymentService {
    // Real payment for production
    public void charge(double amount) {
        // Actual Stripe/PayPal integration
    }
}
```

### Checking Active Profile in Code

```java
@Component
public class ProfileChecker {
    
    @Autowired
    private Environment environment;
    
    @PostConstruct
    public void init() {
        String[] activeProfiles = environment.getActiveProfiles();
        System.out.println("Active profiles: " + Arrays.toString(activeProfiles));
        
        if (environment.acceptsProfiles(Profiles.of("prod"))) {
            System.out.println("Running in PRODUCTION mode!");
        }
    }
}
```

### YAML Profile Configuration (Single File)

```yaml
# application.yml — All profiles in one file using --- separator
spring:
  application:
    name: My Application

---
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    url: jdbc:h2:mem:devdb
server:
  port: 8080

---
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: jdbc:mysql://prod-server:3306/proddb
server:
  port: 80
```

---

## Quick Revision Cheat Sheet

```
Spring Boot = Spring Framework + Auto-configuration + Embedded Server + Starters

Spring Initializr:
  → start.spring.io → Select dependencies → Generate → Run

Project Structure:
  controller/ → Handles HTTP
  service/ → Business logic
  repository/ → Database access
  model/ → Entity classes

Auto-Configuration:
  → Detects classpath dependencies
  → Configures beans automatically
  → @ConditionalOnClass, @ConditionalOnMissingBean
  → You can override any auto-config

@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan

Starters:
  spring-boot-starter-web → REST/Web
  spring-boot-starter-data-jpa → Database ORM
  spring-boot-starter-test → Testing
  spring-boot-starter-security → Auth
  1 starter = multiple compatible dependencies

Application Properties:
  server.port, spring.datasource.*, spring.jpa.*
  @Value("${key}") → inject single property
  @ConfigurationProperties(prefix) → inject group of properties

Profiles:
  application-{profile}.properties
  spring.profiles.active=dev
  @Profile("prod") → Bean only for production
  Switch with: --spring.profiles.active=prod
```

---

**Next: [08-Spring-MVC-Thymeleaf.md](08-Spring-MVC-Thymeleaf.md) — MVC Architecture, Controllers, and Thymeleaf Templating**
