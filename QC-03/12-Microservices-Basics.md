# Microservices Basics - Complete In-Depth Guide

## Table of Contents
- [1. What are Microservices?](#1-what-are-microservices)
- [2. Monolithic vs Microservices](#2-monolithic-vs-microservices)
- [3. Microservices Principles](#3-microservices-principles)
- [4. Building Blocks of Microservices](#4-building-blocks-of-microservices)
- [5. Service Discovery](#5-service-discovery)
- [6. API Gateway](#6-api-gateway)
- [7. Inter-Service Communication](#7-inter-service-communication)
- [8. Data Management in Microservices](#8-data-management-in-microservices)
- [9. Common Patterns](#9-common-patterns)
- [10. Spring Cloud Components](#10-spring-cloud-components)
- [11. Building a Simple Microservice with Spring Boot](#11-building-a-simple-microservice-with-spring-boot)

---

## 1. What are Microservices?

### Definition

**Microservices** is an architectural style where a large application is broken into small, independent services that:
- Do ONE thing well (Single Responsibility)
- Can be deployed independently
- Communicate over the network (usually HTTP/REST)
- Have their own database

### Simple Analogy

```
MONOLITH = One big restaurant doing EVERYTHING
  → One kitchen for Indian, Chinese, Italian, Desserts
  → If the dessert section catches fire, the whole restaurant shuts down!

MICROSERVICES = A food court with SPECIALIZED restaurants
  → Indian restaurant (handles only Indian food)
  → Chinese restaurant (handles only Chinese food)
  → Dessert shop (handles only desserts)
  → If the dessert shop closes, Indian and Chinese still work!
```

---

## 2. Monolithic vs Microservices

### Monolithic Architecture

```
┌─────────────────────────────────────┐
│          MONOLITHIC APP             │
│                                     │
│  ┌──────────┐  ┌──────────┐         │
│  │   User   │  │  Order   │         │
│  │  Module  │  │  Module  │         │
│  └──────────┘  └──────────┘         │
│  ┌──────────┐  ┌──────────┐         │
│  │  Payment │  │  Product │         │
│  │  Module  │  │  Module  │         │
│  └──────────┘  └──────────┘         │
│                                     │
│         ONE Database                │
│         ONE Deployment              │
│         ONE WAR/JAR                 │
└─────────────────────────────────────┘
```

### Microservices Architecture

```
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│  User    │  │  Order   │  │ Payment  │  │ Product  │
│ Service  │  │ Service  │  │ Service  │  │ Service  │
│   :8081  │  │   :8082  │  │   :8083  │  │   :8084  │
│    DB1   │  │    DB2   │  │    DB3   │  │    DB4   │
└──────────┘  └──────────┘  └──────────┘  └──────────┘
     ↕              ↕              ↕              ↕
  ┌──────────────────────────────────────────────────┐
  │              API Gateway (:8080)                 │
  │         (Single entry point for clients)         │
  └──────────────────────────────────────────────────┘
                       ↕
                  ┌──────────┐
                  │  Client  │
                  │ (React,  │
                  │  Mobile) │
                  └──────────┘
```

### Comparison Table

| Feature | Monolithic | Microservices |
|---------|-----------|---------------|
| **Size** | One large codebase | Many small codebases |
| **Deployment** | Deploy everything together | Deploy each service independently |
| **Database** | Single shared database | Each service has its own database |
| **Scaling** | Scale entire app | Scale individual services |
| **Technology** | Same tech stack | Different tech per service |
| **Team** | One large team | Small independent teams |
| **Failure** | One bug can crash everything | One service failing doesn't crash others |
| **Complexity** | Simple to develop initially | More complex (network, data management) |
| **Testing** | Easier (everything in one place) | Harder (need to test interactions) |
| **Best for** | Small/medium apps | Large, complex apps with many teams |

---

## 3. Microservices Principles

### 1. Single Responsibility

```
Each service does ONE thing:
  ✅ User Service → manages users only
  ✅ Order Service → manages orders only
  ✅ Payment Service → handles payments only

  ❌ UserOrderPaymentService → does too many things
```

### 2. Independently Deployable

```
You can deploy User Service without touching Order Service.

Team A updates User Service → Deploy User Service only
Team B updates Payment Service → Deploy Payment Service only

No coordination needed between teams!
```

### 3. Decentralized Data Management

```
Each service owns its data:
  User Service    → users_db (MySQL)
  Order Service   → orders_db (PostgreSQL)
  Product Service → products_db (MongoDB)

Services CANNOT directly access each other's database!
They communicate through APIs.
```

### 4. Design for Failure

```
In a distributed system, things WILL fail.
  → Network can go down
  → A service can crash
  → Database can be slow

Design your services to handle failures:
  → Circuit breakers (stop calling failing service)
  → Retries (try again if it fails)
  → Fallbacks (return default data if service is down)
  → Timeouts (don't wait forever)
```

---

## 4. Building Blocks of Microservices

```
┌───────────────────────────────────────────────┐
│            MICROSERVICES ECOSYSTEM            │
│                                               │
│  ┌──────────────┐    ┌──────────────────┐     │
│  │ API Gateway  │    │ Service Discovery│     │
│  │ (Entry point)│    │ (Find services)  │     │
│  └──────────────┘    └──────────────────┘     │
│                                               │
│  ┌─────────────┐    ┌──────────────────┐      │
│  │Config Server│    │ Circuit Breaker  │      │
│  │(Central     │    │ (Fault tolerance)│      │
│  │ config)     │    │                  │      │
│  └─────────────┘    └──────────────────┘      │
│                                               │
│  ┌─────────────┐    ┌───────────────────┐     │
│  │Load Balancer│    │ Centralized       │     │
│  │(Distribute  │    │ Logging/Monitoring│     │
│  │ traffic)    │    │                   │     │
│  └─────────────┘    └───────────────────┘     │
└───────────────────────────────────────────────┘
```

---

## 5. Service Discovery

### The Problem

```
User Service needs to call Order Service.
But where is Order Service running?

In development:  localhost:8082
In staging:      staging-server:8082
In production:   Could be on ANY of 10 servers!
                 Could be multiple instances!
                 IP addresses change dynamically!

How does User Service FIND Order Service?
→ SERVICE DISCOVERY!
```

### How Service Discovery Works

```
1. Each service REGISTERS itself with a Service Registry
   → "I am Order Service, running at 192.168.1.5:8082"
   → "I am User Service, running at 192.168.1.3:8081"

2. When a service needs to call another:
   → User Service asks Registry: "Where is Order Service?"
   → Registry responds: "It's at 192.168.1.5:8082"
   → User Service calls that address

3. If Order Service moves or scales:
   → New instances register automatically
   → Dead instances are removed automatically

┌──────────┐  "Register me!"   ┌───────────────┐
│  User    │ ────────────────→ │   Service     │
│ Service  │                   │   Registry    │
│          │ "Where is Order?" │   (Eureka)    │
│          │ ────────────────→ │               │
│          │ ←──── "Here!"     │ User: :8081   │
│          │                   │ Order: :8082  │
└──────────┘                   │ Payment: :8083│
                               └───────────────┘
```

### Eureka (Netflix) — Service Registry for Spring

```java
// 1. Eureka Server (separate Spring Boot app)
@SpringBootApplication
@EnableEurekaServer  // This app IS the registry
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}

// application.yml for Eureka Server
// server:
//   port: 8761
// eureka:
//   client:
//     register-with-eureka: false  # Don't register itself
//     fetch-registry: false

// 2. Eureka Client (each microservice)
@SpringBootApplication
@EnableDiscoveryClient  // Register with Eureka
public class UserServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceApplication.class, args);
    }
}

// application.yml for each service
// spring:
//   application:
//     name: user-service
// eureka:
//   client:
//     service-url:
//       defaultZone: http://localhost:8761/eureka/
```

---

## 6. API Gateway

### What is an API Gateway?

A **single entry point** for all client requests. It routes requests to the correct microservice.

```
WITHOUT API Gateway:
  Client needs to know every service's address:
  → GET user:8081/api/users
  → GET order:8082/api/orders
  → GET payment:8083/api/payments
  
  10 services = 10 URLs to manage! 😫

WITH API Gateway:
  Client calls ONE address:
  → GET gateway:8080/api/users    → Routes to User Service
  → GET gateway:8080/api/orders   → Routes to Order Service
  → GET gateway:8080/api/payments → Routes to Payment Service
  
  1 URL to manage! 😊
```

### API Gateway Responsibilities

```
1. ROUTING      → Route requests to correct service
2. LOAD BALANCE → Distribute traffic across instances
3. SECURITY     → Authentication, rate limiting
4. LOGGING      → Log all requests centrally
5. CORS         → Handle cross-origin requests
6. CACHING      → Cache responses
```

### Spring Cloud Gateway

```java
@SpringBootApplication
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

```yaml
# application.yml for Gateway
server:
  port: 8080

spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://USER-SERVICE  # lb = load balanced via Eureka
          predicates:
            - Path=/api/users/**
        
        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/api/orders/**
        
        - id: payment-service
          uri: lb://PAYMENT-SERVICE
          predicates:
            - Path=/api/payments/**
```

---

## 7. Inter-Service Communication

### Two Ways Services Talk

```
1. SYNCHRONOUS (Real-time, waiting for response)
   → REST API calls (HTTP)
   → User Service calls Order Service and WAITS for response

2. ASYNCHRONOUS (Fire and forget, no waiting)
   → Message queues (RabbitMQ, Kafka)
   → Order Service sends message to Payment Service and CONTINUES
   → Payment Service processes the message later
```

### Synchronous — RestTemplate

```java
@Service
public class UserService {
    
    @Autowired
    private RestTemplate restTemplate;
    
    // Call Order Service from User Service
    public List<Order> getUserOrders(Long userId) {
        String url = "http://ORDER-SERVICE/api/orders/user/" + userId;
        ResponseEntity<Order[]> response = restTemplate.getForEntity(url, Order[].class);
        return Arrays.asList(response.getBody());
    }
}

// RestTemplate Bean with load balancing
@Configuration
public class AppConfig {
    @Bean
    @LoadBalanced  // Uses Eureka for service discovery
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

### Synchronous — WebClient (Recommended, Reactive)

```java
@Service
public class UserService {
    
    private final WebClient.Builder webClientBuilder;
    
    public UserService(WebClient.Builder webClientBuilder) {
        this.webClientBuilder = webClientBuilder;
    }
    
    public List<Order> getUserOrders(Long userId) {
        return webClientBuilder.build()
            .get()
            .uri("http://ORDER-SERVICE/api/orders/user/" + userId)
            .retrieve()
            .bodyToFlux(Order.class)
            .collectList()
            .block();  // Block for synchronous result
    }
}
```

### Synchronous — OpenFeign (Simplest, Declarative)

```java
// Just define an interface — Spring implements it automatically!
@FeignClient(name = "ORDER-SERVICE")  // Service name in Eureka
public interface OrderClient {
    
    @GetMapping("/api/orders/user/{userId}")
    List<Order> getOrdersByUserId(@PathVariable Long userId);
    
    @PostMapping("/api/orders")
    Order createOrder(@RequestBody Order order);
}

// Use it like a normal service
@Service
public class UserService {
    
    @Autowired
    private OrderClient orderClient;
    
    public List<Order> getUserOrders(Long userId) {
        return orderClient.getOrdersByUserId(userId);
        // Spring automatically calls the Order Service!
    }
}

// Enable Feign in main class
@SpringBootApplication
@EnableFeignClients
public class UserServiceApplication { }
```

---

## 8. Data Management in Microservices

### Database per Service Pattern

```
RULE: Each microservice owns its data. 
      No service can directly query another service's database!

┌──────────┐     ┌──────────┐     ┌──────────┐
│  User    │     │  Order   │     │ Product  │
│ Service  │     │ Service  │     │ Service  │
└────┬─────┘     └────┬─────┘     └────┬─────┘
     │                │                │
     ↓                ↓                ↓
┌──────────┐     ┌──────────┐     ┌───────────┐
│ users_db │     │ orders_db│     │products_db│
│ (MySQL)  │     │(Postgres)│     │ (MongoDB) │
└──────────┘     └──────────┘     └───────────┘

Each service can even use a DIFFERENT database technology!
```

### The Problem: Cross-Service Data

```
Problem: Order Service needs the user's name and email.
         But user data is in User Service's database!

Solution 1: API Call
  → Order Service calls User Service API to get user details
  → Simple but adds latency

Solution 2: Data Duplication
  → Order Service stores a copy of user name and email
  → Fast but data might get out of sync

Solution 3: Event-Driven
  → When user updates their name, User Service publishes an event
  → Order Service listens and updates its copy
  → Best of both worlds (fast + eventually consistent)
```

---

## 9. Common Patterns

### Circuit Breaker Pattern

```
Problem: If Order Service is down, User Service keeps calling
         and waiting → wastes resources, becomes slow.

Solution: Circuit Breaker (like an electrical circuit breaker)

CLOSED state (normal):
  → Requests pass through normally
  → If too many failures → switch to OPEN

OPEN state (protection):
  → Requests are immediately REJECTED (don't even try)
  → Return fallback response
  → After timeout → switch to HALF-OPEN

HALF-OPEN state (testing):
  → Let a FEW requests through
  → If they succeed → switch to CLOSED
  → If they fail → switch back to OPEN
```

```java
// Resilience4j Circuit Breaker
@Service
public class UserService {
    
    @CircuitBreaker(name = "orderService", fallbackMethod = "getOrdersFallback")
    public List<Order> getUserOrders(Long userId) {
        // Call Order Service
        return orderClient.getOrdersByUserId(userId);
    }
    
    // Fallback method — called when circuit is OPEN
    public List<Order> getOrdersFallback(Long userId, Exception ex) {
        return Collections.emptyList();  // Return empty list instead of error
    }
}
```

### Saga Pattern (Distributed Transactions)

```
Problem: Create an order involves multiple services:
  1. Order Service → Create order
  2. Payment Service → Charge payment
  3. Inventory Service → Reserve stock

  What if payment fails AFTER order is created?
  You need to UNDO the order (compensating transaction).

Saga = A sequence of local transactions where each service:
  → Performs its transaction
  → Publishes an event when done
  → If something fails → runs compensating transactions to undo
```

---

## 10. Spring Cloud Components

| Component | Purpose | Implementation |
|-----------|---------|----------------|
| **Service Discovery** | Find other services | Spring Cloud Netflix Eureka |
| **API Gateway** | Single entry point | Spring Cloud Gateway |
| **Config Server** | Centralized configuration | Spring Cloud Config |
| **Circuit Breaker** | Fault tolerance | Resilience4j |
| **Load Balancer** | Distribute traffic | Spring Cloud LoadBalancer |
| **Feign Client** | Declarative HTTP client | Spring Cloud OpenFeign |
| **Distributed Tracing** | Track requests across services | Micrometer + Zipkin |

### Project Structure for Microservices

```
microservices-project/
├── eureka-server/           ← Service Registry
│   ├── pom.xml
│   └── src/main/java/...
├── api-gateway/             ← API Gateway
│   ├── pom.xml
│   └── src/main/java/...
├── user-service/            ← Microservice 1
│   ├── pom.xml
│   └── src/main/java/...
├── order-service/           ← Microservice 2
│   ├── pom.xml
│   └── src/main/java/...
├── payment-service/         ← Microservice 3
│   ├── pom.xml
│   └── src/main/java/...
└── config-server/           ← Centralized Config
    ├── pom.xml
    └── src/main/java/...
```

---

## 11. Building a Simple Microservice with Spring Boot

### User Service Example

```java
// Main Application
@SpringBootApplication
@EnableDiscoveryClient
public class UserServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceApplication.class, args);
    }
}

// Entity
@Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    // Getters, setters, constructors
}

// Repository
@Repository
public interface UserRepository extends JpaRepository<User, Long> {}

// Service
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    public List<User> getAllUsers() { return userRepository.findAll(); }
    public User getUserById(Long id) { 
        return userRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("User not found")); 
    }
    public User createUser(User user) { return userRepository.save(user); }
}

// Controller
@RestController
@RequestMapping("/api/users")
public class UserController {
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAll() { return userService.getAllUsers(); }
    
    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) { return userService.getUserById(id); }
    
    @PostMapping
    public User create(@RequestBody User user) { return userService.createUser(user); }
}
```

```yaml
# application.yml
spring:
  application:
    name: user-service
  datasource:
    url: jdbc:h2:mem:userdb
  jpa:
    hibernate:
      ddl-auto: update
server:
  port: 8081
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

---

## Quick Revision Cheat Sheet

```
MICROSERVICES:
  = Small, independent services that do ONE thing
  = Each has its OWN database
  = Communicate over HTTP (REST) or messaging (Kafka/RabbitMQ)
  = Deployed independently

MONOLITH vs MICROSERVICES:
  Monolith: One big app, one DB, deploy all or nothing
  Microservices: Many small apps, each with own DB, deploy individually

KEY COMPONENTS:
  Service Discovery (Eureka) → Find services by name
  API Gateway → Single entry point for clients
  Config Server → Centralized configuration
  Circuit Breaker → Handle failures gracefully
  Load Balancer → Distribute traffic

COMMUNICATION:
  Synchronous: RestTemplate / WebClient / OpenFeign
  Asynchronous: RabbitMQ / Apache Kafka

DATA MANAGEMENT:
  Database per service → Each service owns its data
  No direct DB access → Use APIs to get other service's data
  Event-driven → Publish events when data changes

PATTERNS:
  Circuit Breaker → Stop calling failing services
  Saga → Distributed transactions (compensating actions)
  API Gateway → Route and filter requests
  Service Discovery → Dynamically find services

SPRING CLOUD:
  Eureka → Service Registry
  Spring Cloud Gateway → API Gateway
  OpenFeign → Declarative REST client
  Resilience4j → Circuit breaker
  Spring Cloud Config → Config server
```

---

