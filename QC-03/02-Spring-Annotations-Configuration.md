# Spring Annotations, Configuration & SpEL - Complete In-Depth Guide

## Table of Contents
- [1. Annotation Configuration](#1-annotation-configuration)
- [2. Java Configuration (@Configuration, @Bean)](#2-java-configuration-configuration-bean)
- [3. @PropertySource and @Value](#3-propertysource-and-value)
- [4. Application Properties](#4-application-properties)
- [5. Spring Expression Language (SpEL)](#5-spring-expression-language-spel)

---

## 1. Annotation Configuration

### What is Annotation Configuration?

Instead of writing XML files to tell Spring about your beans, you use **annotations directly on your Java classes**. This is the modern and preferred way.

### The Old Way: XML Configuration

Before annotations, you had to write XML:

```xml
<!-- beans.xml — the old way -->
<beans>
    <bean id="petrolEngine" class="com.example.PetrolEngine"/>
    <bean id="car" class="com.example.Car">
        <constructor-arg ref="petrolEngine"/>
    </bean>
</beans>
```

### The Modern Way: Annotations

```java
@Component  // This replaces the <bean> tag in XML
public class PetrolEngine implements Engine {
    public void start() {
        System.out.println("Petrol engine started");
    }
}

@Component
public class Car {
    private final Engine engine;
    
    @Autowired  // This replaces <constructor-arg> in XML
    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

### How Does Spring Know Where to Look for Annotations?

Spring uses **Component Scanning**. You tell it a base package, and it scans all classes in that package and sub-packages.

```java
// Way 1: Using @ComponentScan
@Configuration
@ComponentScan("com.example")  // Scan this package and all sub-packages
public class AppConfig {
}

// Way 2: @SpringBootApplication does it automatically
@SpringBootApplication  // Scans the package where this class is located + sub-packages
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

### Under the Hood: How Component Scanning Works

```
Step 1: Spring reads @ComponentScan or @SpringBootApplication
Step 2: Determines the base package (e.g., com.example)
Step 3: Uses ClassLoader to find all .class files in that package
Step 4: Reads annotations on each class
Step 5: For classes with @Component/@Service/@Repository/@Controller:
        → Creates a BeanDefinition
        → Registers it in the container
Step 6: Creates instances and injects dependencies

com.example/
├── MyApp.java              ← @SpringBootApplication (BASE PACKAGE)
├── config/
│   └── AppConfig.java      ← @Configuration ✅ SCANNED
├── service/
│   └── UserService.java    ← @Service ✅ SCANNED
├── model/
│   └── User.java           ← No annotation ❌ NOT a bean (just a POJO)
└── util/
    └── Helper.java          ← @Component ✅ SCANNED
    
com.other/                   ← DIFFERENT package
└── OtherService.java        ← @Service ❌ NOT SCANNED (different package)
```

### Customizing Component Scan

```java
@Configuration
@ComponentScan(
    basePackages = {"com.example.service", "com.example.repository"},
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.REGEX,
        pattern = "com\\.example\\.service\\.legacy\\..*"
    )
)
public class AppConfig {
}
```

---

## 2. Java Configuration (@Configuration, @Bean)

### What is Java Configuration?

Sometimes you need **more control** over how a bean is created. Maybe it's a third-party class (you can't add `@Component` to it), or creating it requires complex logic. In these cases, you use `@Configuration` and `@Bean`.

### @Configuration

`@Configuration` marks a class as a **source of bean definitions**. Think of it as a **factory class** that creates beans.

### @Bean

`@Bean` marks a **method** whose return value will be registered as a bean in the Spring container.

### Code Example

```java
@Configuration  // This class provides bean definitions
public class AppConfig {
    
    // This method creates and returns a bean
    // The bean name is the METHOD NAME: "dataSource"
    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        ds.setUsername("root");
        ds.setPassword("password");
        ds.setMaximumPoolSize(10);
        return ds;
    }
    
    // Custom bean name
    @Bean("emailService")
    public NotificationService emailNotification() {
        return new EmailNotificationService("smtp.gmail.com", 587);
    }
    
    // Bean that depends on another bean
    @Bean
    public UserRepository userRepository(DataSource dataSource) {
        // Spring automatically passes the dataSource bean defined above
        return new UserRepository(dataSource);
    }
}
```

### Under the Hood: How @Configuration Works

```
Regular @Component class:       @Configuration class:
  → Creates normal object         → Creates CGLIB PROXY
  
What does this mean?

@Configuration
public class AppConfig {
    
    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
    
    @Bean
    public UserRepository userRepo() {
        // This calls dataSource() method
        // WITHOUT @Configuration: creates a NEW DataSource each time ❌
        // WITH @Configuration: returns the SAME singleton bean ✅
        return new UserRepository(dataSource());
    }
    
    @Bean
    public OrderRepository orderRepo() {
        // Also calls dataSource()
        // Returns the SAME DataSource instance (singleton!)
        return new OrderRepository(dataSource());
    }
}

CGLIB Proxy intercepts the dataSource() call:
  → First call: creates object, caches it
  → Second call: returns CACHED object (same instance!)
```

### @Bean vs @Component

| Feature | @Bean | @Component |
|---------|-------|------------|
| Where | On a method inside @Configuration | On the class itself |
| For what | Third-party classes, complex creation | Your own classes |
| Control | Full control over creation | Simple creation |
| Name | Method name (by default) | Class name (lowercase first letter) |

### When to Use @Bean

```java
// Scenario 1: Third-party library class (you can't modify the source code)
@Bean
public ObjectMapper objectMapper() {
    ObjectMapper mapper = new ObjectMapper();
    mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
    mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd"));
    return mapper;
}

// Scenario 2: Complex creation logic
@Bean
public RestTemplate restTemplate() {
    HttpComponentsClientHttpRequestFactory factory = 
        new HttpComponentsClientHttpRequestFactory();
    factory.setConnectTimeout(5000);
    factory.setReadTimeout(5000);
    return new RestTemplate(factory);
}

// Scenario 3: Conditional bean creation
@Bean
@Profile("production")
public DataSource productionDataSource() {
    // Production database config
    return new HikariDataSource(prodConfig);
}

@Bean
@Profile("development")
public DataSource devDataSource() {
    // In-memory H2 for development
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .build();
}
```

### @Bean Lifecycle Control

```java
@Bean(initMethod = "connect", destroyMethod = "disconnect")
public DatabasePool databasePool() {
    return new DatabasePool("localhost", 5432);
}

// Equivalent to:
public class DatabasePool {
    public void connect() {
        // Called after bean creation (like @PostConstruct)
        System.out.println("Connected to database");
    }
    
    public void disconnect() {
        // Called before bean destruction (like @PreDestroy)
        System.out.println("Disconnected from database");
    }
}
```

---

## 3. @PropertySource and @Value

### The Problem

You don't want to hardcode values like database URLs, API keys, or port numbers in your Java code. If something changes, you'd have to recompile.

### The Solution: External Properties

Store configuration in **property files** and inject them into your beans.

### @PropertySource

Tells Spring to **load properties from a specific file**.

```properties
# src/main/resources/database.properties
db.url=jdbc:mysql://localhost:3306/mydb
db.username=root
db.password=secret123
db.pool.size=10
```

```java
@Configuration
@PropertySource("classpath:database.properties")  // Load this properties file
public class DatabaseConfig {
    
    @Value("${db.url}")      // Inject the value of "db.url" from properties
    private String dbUrl;
    
    @Value("${db.username}")
    private String dbUsername;
    
    @Value("${db.password}")
    private String dbPassword;
    
    @Value("${db.pool.size}")
    private int poolSize;
    
    @Bean
    public DataSource dataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl(dbUrl);
        ds.setUsername(dbUsername);
        ds.setPassword(dbPassword);
        ds.setMaximumPoolSize(poolSize);
        return ds;
    }
}
```

### @Value Annotation

`@Value` injects values from properties, environment variables, or hardcoded values into your beans.

```java
@Component
public class AppSettings {
    
    // From properties file
    @Value("${app.name}")
    private String appName;
    
    // With default value (if property not found, use "8080")
    @Value("${server.port:8080}")
    private int serverPort;
    
    // Hardcoded value
    @Value("Hello World")
    private String greeting;
    
    // From environment variable
    @Value("${JAVA_HOME}")
    private String javaHome;
    
    // Boolean conversion
    @Value("${app.debug:false}")
    private boolean debugMode;
    
    // List injection (app.servers=server1,server2,server3)
    @Value("${app.servers}")
    private List<String> servers;
    
    // Inject with constructor
    public AppSettings(@Value("${app.version:1.0}") String version) {
        System.out.println("App version: " + version);
    }
}
```

### Under the Hood: How @Value Works

```
1. Spring starts up
2. @PropertySource loads the properties file into Environment
3. When creating a bean, Spring finds @Value annotations
4. Resolves the placeholder: "${db.url}" → looks up "db.url" in Environment
5. Converts the String value to the target type (String, int, boolean, etc.)
6. Injects the value into the field/parameter

PropertySource → Environment → PropertyResolver → @Value injection
```

### Multiple Property Sources

```java
@Configuration
@PropertySource("classpath:application.properties")
@PropertySource("classpath:database.properties")
@PropertySource("classpath:mail.properties")
public class AppConfig {
    // All properties from all three files are available
}

// Or using @PropertySources
@PropertySources({
    @PropertySource("classpath:application.properties"),
    @PropertySource("classpath:database.properties")
})
public class AppConfig {
}
```

---

## 4. Application Properties

### What is application.properties?

In Spring Boot, `application.properties` (or `application.yml`) is the **main configuration file**. Spring Boot automatically loads it — no need for `@PropertySource`.

### Location

```
src/
└── main/
    └── resources/
        └── application.properties    ← Spring Boot loads this automatically
```

### Common Properties

```properties
# ===== SERVER =====
server.port=8080
server.servlet.context-path=/api

# ===== DATABASE =====
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# ===== JPA / HIBERNATE =====
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# ===== LOGGING =====
logging.level.root=INFO
logging.level.com.example=DEBUG

# ===== CUSTOM PROPERTIES =====
app.name=My Awesome App
app.version=2.0
app.max-retry=3
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
  name: My Awesome App
  version: 2.0
```

### Profile-Specific Properties

You can have different configurations for different environments:

```
src/main/resources/
├── application.properties              ← Common properties
├── application-dev.properties           ← Development specific
├── application-prod.properties          ← Production specific
└── application-test.properties          ← Testing specific
```

```properties
# application.properties
spring.profiles.active=dev   # Activate the "dev" profile

# application-dev.properties
server.port=8080
spring.datasource.url=jdbc:h2:mem:testdb   # In-memory database for dev

# application-prod.properties
server.port=80
spring.datasource.url=jdbc:mysql://prod-server:3306/mydb
```

### Property Priority Order (Highest to Lowest)

```
1. Command-line arguments (--server.port=9090)
2. JNDI attributes
3. Java System properties (-Dserver.port=9090)
4. OS environment variables (SERVER_PORT=9090)
5. Profile-specific properties (application-{profile}.properties)
6. application.properties
7. @PropertySource annotations
8. Default properties
```

---

## 5. Spring Expression Language (SpEL)

### What is SpEL?

SpEL is a powerful expression language for querying and manipulating objects at **runtime**. Think of it as a mini programming language you can use inside annotations and configuration.

### Why SpEL?

Regular `@Value` can only read properties:
```java
@Value("${app.name}")  // Just reads a property value
```

SpEL can do calculations, call methods, access lists, and much more:
```java
@Value("#{2 + 3}")          // Calculates: 5
@Value("#{T(Math).PI}")     // Calls static method: 3.14159...
@Value("#{systemProperties['user.home']}")  // Access system properties
```

### SpEL Syntax

SpEL expressions are written inside `#{ }` (not `${ }`).

- `${ }` → Property placeholder (reads from properties file)
- `#{ }` → SpEL expression (evaluates an expression)

### 5.1 Literal Expressions

```java
@Component
public class SpELDemo {
    
    @Value("#{42}")           // Integer
    private int number;
    
    @Value("#{'Hello SpEL'}")  // String
    private String message;
    
    @Value("#{true}")          // Boolean
    private boolean active;
    
    @Value("#{3.14}")          // Double
    private double pi;
    
    @Value("#{null}")          // Null
    private Object nothing;
}
```

### 5.2 Arithmetic Operations

```java
@Value("#{10 + 5}")     // 15
private int sum;

@Value("#{10 - 5}")     // 5
private int diff;

@Value("#{10 * 5}")     // 50
private int product;

@Value("#{10 / 3}")     // 3
private int quotient;

@Value("#{10 % 3}")     // 1 (remainder)
private int remainder;

@Value("#{2 ^ 10}")     // 1024 (power)
private int power;
```

### 5.3 Comparison and Logical Operators

```java
@Value("#{10 > 5}")      // true
private boolean greater;

@Value("#{10 == 10}")    // true
private boolean equal;

@Value("#{10 > 5 and 3 < 7}")   // true (AND)
private boolean both;

@Value("#{10 > 50 or 3 < 7}")   // true (OR)
private boolean either;

@Value("#{!true}")       // false (NOT)
private boolean negated;

// You can also use: eq, ne, lt, gt, le, ge, and, or, not
@Value("#{10 gt 5}")     // true
private boolean gtExample;
```

### 5.4 Variables and References to Other Beans

```java
@Component("myService")
public class MyService {
    
    private String serviceName = "UserService";
    
    public String getServiceName() {
        return serviceName;
    }
    
    public int calculateAge(int birthYear) {
        return 2026 - birthYear;
    }
}

@Component
public class AnotherComponent {
    
    // Reference another bean by name
    @Value("#{myService.serviceName}")
    private String name;  // "UserService"
    
    // Call a method on another bean
    @Value("#{myService.calculateAge(1995)}")
    private int age;  // 31
    
    // Chain method calls
    @Value("#{myService.serviceName.toUpperCase()}")
    private String upperName;  // "USERSERVICE"
    
    // String length
    @Value("#{myService.serviceName.length()}")
    private int nameLength;  // 11
}
```

### 5.5 Accessing System Properties and Environment

```java
@Value("#{systemProperties['user.home']}")
private String userHome;  // /home/kunal

@Value("#{systemProperties['java.version']}")
private String javaVersion;  // 17.0.1

@Value("#{systemEnvironment['PATH']}")
private String path;

@Value("#{systemProperties['os.name']}")
private String osName;  // Linux
```

### 5.6 Type References (T operator)

Use `T()` to reference a Java class and call static methods or access constants.

```java
// Access static fields
@Value("#{T(java.lang.Math).PI}")
private double pi;  // 3.141592653589793

@Value("#{T(java.lang.Integer).MAX_VALUE}")
private int maxInt;  // 2147483647

// Call static methods
@Value("#{T(java.lang.Math).random()}")
private double randomValue;

@Value("#{T(java.lang.Math).max(10, 20)}")
private int maxValue;  // 20

// Use with date
@Value("#{T(java.time.LocalDate).now()}")
private LocalDate today;
```

### 5.7 Ternary (Conditional) Operator

```java
// condition ? trueValue : falseValue
@Value("#{${app.debug:false} ? 'DEBUG MODE' : 'PRODUCTION MODE'}")
private String mode;

// Elvis operator (shorthand for null check)
// If value is null, use default
@Value("#{${app.name} ?: 'Default App'}")
private String appName;
```

### 5.8 Collections with SpEL

```java
@Component
public class CollectionSpEL {
    
    // Create a list
    @Value("#{{1, 2, 3, 4, 5}}")
    private List<Integer> numbers;
    
    // Create a map
    @Value("#{{key1: 'value1', key2: 'value2'}}")
    private Map<String, String> map;
    
    // Access list element
    @Value("#{myBean.items[0]}")
    private String firstItem;
    
    // Access map value
    @Value("#{myBean.config['timeout']}")
    private String timeout;
    
    // Filter a list (selection) — get all items > 10
    @Value("#{myBean.numbers.?[#this > 10]}")
    private List<Integer> filteredNumbers;
    
    // Get first match
    @Value("#{myBean.numbers.^[#this > 10]}")
    private Integer firstOver10;
    
    // Get last match
    @Value("#{myBean.numbers.$[#this > 10]}")
    private Integer lastOver10;
    
    // Project (transform) — get all names from list of users
    @Value("#{myBean.users.![name]}")
    private List<String> userNames;
}
```

### 5.9 SpEL with @Value — Combining Property Placeholder and SpEL

```java
// Combine ${} (property) with #{} (SpEL)
@Value("#{${app.max-connections:10} * 2}")
private int doubledConnections;  // Reads property, then multiplies by 2

@Value("#{'${app.name}'.toUpperCase()}")
private String upperAppName;  // Reads property, then converts to uppercase

@Value("#{${server.port:8080} > 1024 ? 'Non-privileged' : 'Privileged'}")
private String portType;
```

### 5.10 SpEL in Annotations (Other Than @Value)

```java
// In @Cacheable
@Cacheable(value = "users", condition = "#id > 10")
public User findUser(Long id) { ... }

// In @PreAuthorize (Spring Security)
@PreAuthorize("#user.name == authentication.name")
public void updateUser(User user) { ... }

// In @ConditionalOnExpression
@Bean
@ConditionalOnExpression("${app.feature.enabled:true}")
public FeatureService featureService() { ... }

// In @Scheduled
@Scheduled(fixedDelayString = "#{${app.poll-interval:5000}}")
public void pollForUpdates() { ... }
```

### Complete SpEL Example

```java
@Component
public class AppInfo {
    
    private List<String> features = Arrays.asList("login", "dashboard", "reports", "admin");
    private Map<String, Integer> limits = Map.of("maxUsers", 100, "maxConnections", 50);
    
    // Getters...
    public List<String> getFeatures() { return features; }
    public Map<String, Integer> getLimits() { return limits; }
}

@Component
public class SpELShowcase {
    
    // Bean reference
    @Value("#{appInfo.features.size()}")
    private int featureCount;  // 4
    
    // List filtering
    @Value("#{appInfo.features.?[#this.startsWith('d')]}")
    private List<String> dFeatures;  // ["dashboard"]
    
    // Map access
    @Value("#{appInfo.limits['maxUsers']}")
    private int maxUsers;  // 100
    
    // Conditional
    @Value("#{appInfo.features.size() > 3 ? 'Feature-rich' : 'Basic'}")
    private String appType;  // "Feature-rich"
    
    // String manipulation
    @Value("#{appInfo.features[0].toUpperCase()}")
    private String firstFeature;  // "LOGIN"
    
    // Math
    @Value("#{appInfo.limits['maxUsers'] * appInfo.limits['maxConnections']}")
    private int product;  // 5000
    
    @PostConstruct
    public void printInfo() {
        System.out.println("Features: " + featureCount);
        System.out.println("D-Features: " + dFeatures);
        System.out.println("Max Users: " + maxUsers);
        System.out.println("App Type: " + appType);
    }
}
```

---

## Quick Revision Cheat Sheet

```
CONFIGURATION STYLES:
  XML Config     → Old way, barely used now
  Annotation     → @Component + @ComponentScan (MODERN)
  Java Config    → @Configuration + @Bean (for complex or 3rd-party beans)

@Configuration  = "This class provides bean definitions"
@Bean           = "This method returns a bean" (used for 3rd-party classes)
@ComponentScan  = "Scan these packages for @Component classes"

@PropertySource = "Load this .properties file"
@Value("${key}")       = Inject property value
@Value("${key:default}") = Inject with fallback

Application Properties:
  application.properties → Loaded automatically by Spring Boot
  application-{profile}.properties → Profile-specific configs

SpEL (Spring Expression Language):
  ${ } → Property placeholder (reads value)
  #{ } → SpEL expression (evaluates expression)
  
  #{10 + 5}                       → Arithmetic
  #{myBean.method()}              → Call bean methods
  #{T(Math).PI}                   → Static access
  #{systemProperties['key']}      → System properties
  #{cond ? 'yes' : 'no'}         → Ternary
  #{list.?[#this > 5]}           → Filter list
  #{list.![name]}                → Project/transform
```

---

**Next: [03-Maven.md](03-Maven.md) — Maven Project Structure, POM, Build Lifecycle, and Plugins**
