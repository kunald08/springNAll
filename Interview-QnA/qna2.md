# Spring Boot Interview Answers - Plain English

## 92. What is the difference between Spring and Spring Boot? Why did we move from Spring to Spring Boot?

Spring is the core framework. In old Spring, we had to do a lot of manual configuration like XML setup, bean configuration, server setup, and dependency management.

Spring Boot is built on top of Spring. It reduces configuration work by giving auto-configuration, starter dependencies, and embedded servers like Tomcat.

We moved from Spring to Spring Boot because development becomes faster, configuration becomes easier, and project setup becomes much cleaner.

## 93. How does a Spring Boot application start? Walk through the process.

When I run the main class, the `main()` method calls `SpringApplication.run()`. Then Spring Boot starts the application context, scans packages, creates beans, applies auto-configuration, loads properties, starts the embedded server, and finally the application becomes ready to accept requests.

## 94. What annotations are inside @SpringBootApplication?

`@SpringBootApplication` is a combination of three main annotations:

- `@Configuration`
- `@EnableAutoConfiguration`
- `@ComponentScan`

## 95. What is IoC (Inversion of Control)?

IoC means control is given to the Spring container. Instead of creating and managing objects myself, Spring creates and manages them for me.

## 96. What is Dependency Injection? What are its types?

Dependency Injection means Spring gives the required object to a class instead of the class creating it itself.

The main types are:

- constructor injection
- setter injection
- field injection

In real projects, I prefer constructor injection because it is cleaner and easier to test.

## 97. What is the difference between IoC container and Dependency Injection?

IoC container is the Spring container that manages object creation and lifecycle.

Dependency Injection is the technique the container uses to give dependencies to classes.

So IoC is the bigger concept, and DI is one way to achieve it.

## 98. What is the difference between BeanFactory and ApplicationContext?

`BeanFactory` is the basic container. It provides basic bean management.

`ApplicationContext` is advanced and commonly used in Spring Boot. It supports bean management plus features like event handling, message source, AOP support, and easier integration.

In Spring Boot, we normally use `ApplicationContext`.

## 99. What are Bean scopes in Spring Boot?

The common bean scopes are:

- `singleton` - one object for the whole container
- `prototype` - new object every time requested
- `request` - one object per HTTP request
- `session` - one object per HTTP session
- `application` - one object for the whole web application

Most Spring beans are singleton by default.

## 100. What is loose coupling in Spring Boot? How is it achieved?

Loose coupling means classes should depend on abstraction, not on concrete implementation. In Spring Boot, it is achieved using interfaces and dependency injection. This makes code easier to change, test, and maintain.

## 101. What is MVC architecture? Explain the role of each layer.

MVC means Model View Controller.

- Model holds the data
- View shows the UI to the user
- Controller handles requests and connects view with business logic

In Spring Boot, the controller receives the request, the service layer handles logic, and the view or response goes back to the user.

## 102. What is Maven? Explain Maven architecture.

Maven is a build and dependency management tool. It helps manage project libraries, compile code, run tests, and build the application.

Maven mainly works using:

- `pom.xml` for configuration
- local repository in the system
- central repository to download dependencies
- plugins for tasks like compile, test, package

## 103. What is Postman? How did you use it in your project?

Postman is an API testing tool. I used it to test backend endpoints by sending GET, POST, PUT, and DELETE requests. It helps verify request data, response data, status codes, and authentication behavior.

## 104. What is payment gateway integration? How is it done?

Payment gateway integration means connecting an application with a payment service so users can make online payments.

Usually it is done by:

- creating account with payment provider
- getting API keys
- sending payment request from backend
- redirecting or opening payment page
- verifying payment response or webhook
- updating order or payment status in database

## 105. What is @SpringBootApplication? What does it combine?

`@SpringBootApplication` is the main annotation used in the Spring Boot main class. It combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.

## 106. What is @ComponentScan? What does it do?

`@ComponentScan` tells Spring where to scan for classes like `@Controller`, `@Service`, `@Repository`, and `@Component`. After scanning, Spring creates beans for them.

## 107. What is @Autowired? Why do we use it?

`@Autowired` is used to inject a dependency automatically. We use it so Spring can provide the required bean instead of creating it manually.

## 108. What is @Qualifier? When is it used?

`@Qualifier` is used when there are multiple beans of the same type. It tells Spring exactly which bean should be injected.

## 109. What is the difference between @Controller and @RestController?

`@Controller` is mainly used in MVC applications where we return view names like HTML or Thymeleaf pages.

`@RestController` is used when we return data directly, usually JSON.

## 110. What is @ResponseBody? Why is it used?

`@ResponseBody` tells Spring to send the return value directly in the HTTP response body instead of treating it as a view name. It is mainly used for JSON or plain text responses.

## 111. What is the difference between @RequestMapping, @GetMapping, and @PostMapping?

`@RequestMapping` is a general mapping annotation.

`@GetMapping` is specifically for HTTP GET requests.

`@PostMapping` is specifically for HTTP POST requests.

So `@GetMapping` and `@PostMapping` are more specific and cleaner.

## 112. What is @PathVariable? Write an example.

`@PathVariable` is used to read a value from the URL path.

Example:

```java
@GetMapping("/users/{id}")
public String getUser(@PathVariable Long id) {
    return "User id is " + id;
}
```

## 113. What is @RequestParam? Write an example.

`@RequestParam` is used to read query parameter values from the request.

Example:

```java
@GetMapping("/search")
public String search(@RequestParam String keyword) {
    return "Searching for " + keyword;
}
```

## 114. What are @Service, @Repository, and @Component used for?

`@Service` is used for business logic classes.

`@Repository` is used for database access classes.

`@Component` is a general-purpose Spring bean annotation.

These annotations help Spring identify and manage classes as beans.

## 115. What is @Transactional? What does it do?

`@Transactional` is used to manage database transactions. It makes sure all operations inside the method happen as one unit. If something fails, the transaction can roll back.

## 116. What is @ExceptionHandler? How do you use it globally?

`@ExceptionHandler` is used to handle specific exceptions in Spring.

For global handling, I use it inside a class marked with `@ControllerAdvice` or `@RestControllerAdvice`. That way one place can handle exceptions for the whole application.

## 117. What is @Entity and @Table? What is the difference?

`@Entity` tells JPA that the class is a database entity.

`@Table` is used to specify the table name and table-related settings.

So `@Entity` marks the class as persistent, and `@Table` gives table mapping details.

## 118. What is @Id and @SequenceGenerator?

`@Id` marks the primary key field.

`@SequenceGenerator` is used to generate ID values using a database sequence.

## 119. What is @Value? How do you inject a property from application.properties?

`@Value` is used to read a value from properties file.

Example:

```java
@Value("${server.port}")
private String port;
```

## 120. What is @CrossOrigin? Why is it used?

`@CrossOrigin` is used to allow requests from another origin, like when frontend and backend run on different ports or domains. It helps solve CORS issues.

## 121. What is @NotBlank? Where did you use it in your project?

`@NotBlank` is a validation annotation. It makes sure a string is not null, not empty, and not only spaces.

In project work, I used such validation annotations in DTOs or form fields like name, email, title, or required input fields.

## 122. What is @EnableAutoConfiguration?

`@EnableAutoConfiguration` tells Spring Boot to configure beans automatically based on the dependencies available in the project.

## 123. What are all the annotations you used in your project?

In my project, I used annotations like:

- `@SpringBootApplication`
- `@Controller`
- `@RestController`
- `@Service`
- `@Repository`
- `@Component`
- `@Autowired`
- `@Qualifier`
- `@GetMapping`
- `@PostMapping`
- `@RequestMapping`
- `@PathVariable`
- `@RequestParam`
- `@RequestBody`
- `@ResponseBody`
- `@Entity`
- `@Table`
- `@Id`
- `@GeneratedValue`
- `@SequenceGenerator`
- `@Transactional`
- `@ControllerAdvice`
- `@ExceptionHandler`
- `@Value`
- `@CrossOrigin`
- validation annotations like `@NotBlank`, `@NotNull`, `@Email`

## 124. What is Spring Data JPA? How is it different from JDBC?

Spring Data JPA is a higher-level framework for database access. It reduces boilerplate code and works with entities and repositories.

JDBC is lower level. In JDBC, I have to write SQL, manage connections, handle result sets, and convert rows manually.

With Spring Data JPA, most of that work is simplified.

## 125. What is ORM? What is Hibernate?

ORM means Object Relational Mapping. It maps Java classes to database tables.

Hibernate is the most popular JPA implementation. It handles the mapping between Java objects and database records.

## 126. What are fetch types in JPA? What is the difference between EAGER and LAZY?

The main fetch types are `EAGER` and `LAZY`.

`EAGER` means related data is loaded immediately.

`LAZY` means related data is loaded only when needed.

`LAZY` is usually better for performance when related data is not always required.

## 127. What is the Entity lifecycle in JPA?

The main entity states are:

- transient - object created but not managed
- persistent - managed by JPA and connected to the database context
- detached - was managed before, but no longer managed
- removed - marked for deletion

## 128. What are the methods in EntityManager?

Some common `EntityManager` methods are:

- `persist()`
- `find()`
- `merge()`
- `remove()`
- `createQuery()`
- `createNativeQuery()`
- `flush()`
- `detach()`

## 129. What is @OneToMany and @ManyToOne? How do you use mappedBy?

`@OneToMany` means one parent can have many child records.

`@ManyToOne` means many child records belong to one parent.

`mappedBy` is used on the non-owning side to tell JPA which field owns the relationship.

## 130. What relationships have you used in your project?

In my project, I used relationships like one-to-many, many-to-one, and one-to-one depending on the module. For example, one user can have related profile data, one employer can have many jobs, and one job can have many applications.

## 131. What is the difference between JPQL and Native Query?

JPQL works with entity names and object fields.

Native query works with actual database tables and SQL syntax.

I use JPQL when I want database-independent queries, and native query when I need full SQL control.

## 132. How do you write custom queries in JPA?

I can write custom queries using the `@Query` annotation inside repository interfaces. I can write either JPQL or native SQL there.

## 133. What parameters do you use in Repository to communicate with the DB?

In Spring Data JPA, repository interfaces usually extend something like `JpaRepository<EntityClass, IdType>`. So the two main parameters are:

- entity class
- primary key type

Example: `JpaRepository<User, Long>`

## 134. What is @SequenceGenerator? Where did you use it?

`@SequenceGenerator` is used to generate primary key values using a database sequence. I used it in entity classes where IDs were generated using sequence-based strategy.

## 135. Explain Spring Security in detail — Authentication and Authorization.

Spring Security is used to secure the application.

Authentication means verifying who the user is, like checking username and password.

Authorization means deciding what the user is allowed to access after login.

In a Spring Boot project, Spring Security intercepts requests, checks login credentials, creates security context, and then applies access rules based on roles or authorities.

## 136. What is JWT? What does it stand for? Why do we use it?

JWT stands for JSON Web Token. It is used to securely transfer user identity and claims between client and server.

We use it mainly in stateless authentication, especially in REST APIs.

## 137. Where is JWT used in your project?

In the RevHire project we did not use JWT. We used Spring Security with session-based authentication. So if an interviewer asks this, I would answer honestly that JWT was not used in this project.

## 138. How and where is the JWT token generated?

In general, JWT is generated after successful login, usually in the authentication service or controller. The server creates the token with user details and signs it with a secret key.

But in my project, we used session-based login, so no JWT token was generated.

## 139. How are dashboards assigned based on roles?

After successful login, the application checks the user role. Based on that role, the user is redirected to the correct dashboard or landing page.

For example, seeker users go to job search pages, and employer users go to employer dashboard pages.

## 140. How is role-based access control implemented in your project?

Role-based access control is implemented using Spring Security. User roles are stored in the user entity, converted into authorities, and then protected routes are configured using security rules like role-based URL access.

## 141. How do you restrict a class from being extended in the context of security?

I can restrict a class from being extended by making it `final`.

## 142. What is application.properties? What do you configure in it?

`application.properties` is the main configuration file in Spring Boot. I use it to configure things like:

- server port
- database connection
- JPA settings
- logging
- file upload settings
- mail settings
- profile settings

## 143. How do you configure database connection in Spring Boot?

Database connection is configured in `application.properties` or profile-specific properties using:

- datasource URL
- username
- password
- driver class name
- JPA and Hibernate settings

## 144. Can you connect JS or HTML files in application.properties?

Not directly in the same way as database properties. But we can configure things related to static file locations, template prefix, suffix, or resource handling in properties.

## 145. How do you create a Model in Spring Boot?

In Spring MVC, I create a model by taking `Model` as a method parameter in the controller, then adding attributes using `model.addAttribute()`.

## 146. What is @PageController?

There is no standard Spring annotation called `@PageController`. Usually for returning pages we use `@Controller`.

## 147. What are design patterns used in Spring? Explain Singleton and Prototype.

Spring uses many design patterns like singleton, prototype, factory, proxy, MVC, template pattern, and dependency injection pattern.

Singleton means only one bean instance is created in the container.

Prototype means a new bean instance is created every time it is requested.

## 148. What build tool did you use in your project? Why?

I used Maven in my project. I used it because it manages dependencies well, gives a standard project structure, supports build lifecycle clearly, and works very smoothly with Spring Boot.
