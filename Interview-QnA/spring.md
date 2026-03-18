# Spring Boot Interview Questions and Answers

## Spring Boot Interview Questions for Freshers

### 1. What is Spring Boot?
Spring Boot is built on top of the Spring Framework and is used to create standalone, production-ready applications with minimal configuration. Its main goal is to reduce boilerplate setup and make development faster. It also comes with embedded servers like Tomcat, so in many cases we do not need to deploy the application separately on an external server.

### 2. What are the features of Spring Boot?
Spring Boot provides many useful features out of the box. It supports auto-configuration, starter dependencies, embedded servers, actuator endpoints, and externalized configuration. It also makes dependency management easier and helps developers quickly build and run applications without spending too much time on manual setup.

### 3. What are the advantages of using Spring Boot?
The biggest advantage of Spring Boot is that it speeds up development. It reduces boilerplate code, provides sensible default configurations, and makes project setup much easier. It also supports production-ready features like health checks and metrics, and it works well for scalable applications such as REST APIs and microservices.

### 4. Define the key components of Spring Boot.
Some key components of Spring Boot are starter dependencies, auto-configuration, actuator, Spring Boot CLI, and embedded servers. Starter dependencies simplify dependency setup, auto-configuration reduces manual configuration, actuator helps with monitoring, CLI helps with quick project operations, and embedded servers make deployment easier.

### 5. Why do we prefer Spring Boot over Spring?
We prefer Spring Boot because it simplifies development compared to traditional Spring. In Spring, we usually need to configure many things manually, but Spring Boot provides auto-configuration, starter dependencies, and embedded servers. This makes project setup faster, cleaner, and more suitable for modern application development.

### 6. Explain the internal working of Spring Boot.
Internally, Spring Boot starts by loading the main class annotated with `@SpringBootApplication`. It creates the Spring application context, scans components, applies auto-configuration based on dependencies present in the classpath, and initializes all required beans. If it is a web application, it also starts the embedded server and makes the application ready to handle requests.

### 7. What are Spring Boot starter dependencies?
Starter dependencies are pre-configured dependency packages provided by Spring Boot. Instead of adding many related libraries one by one, we can add a single starter like `spring-boot-starter-web` or `spring-boot-starter-data-jpa`. This makes dependency management easier and ensures compatible libraries are included together.

### 8. How does a Spring application get started?
A Spring Boot application usually starts from the `main()` method, where we call `SpringApplication.run()`. The main class is generally annotated with `@SpringBootApplication`. When this runs, Spring Boot creates the application context, loads the configuration, initializes beans, and starts the embedded server if it is a web application.

### 9. What does `@SpringBootApplication` do internally?
`@SpringBootApplication` is a convenience annotation that combines three annotations: `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`. Because of this, it marks the class as a configuration class, enables automatic configuration based on dependencies, and scans components in the package and sub-packages. In short, it is the main annotation that bootstraps a Spring Boot application.

### 10. What is Spring Initializr?
Spring Initializr is a tool used to generate the basic structure of a Spring Boot project. It allows us to choose the project type, language, dependencies, and build tool, and then it creates the starter project for us. This saves time and makes project setup much easier.

### 11. What are Spring Boot CLI and the most used CLI commands?
Spring Boot CLI is a command-line tool that helps create, run, and test Spring Boot applications quickly. It is especially useful for fast prototyping and simple project operations. Some commonly used CLI commands are `run`, `test`, `jar`, `war`, `init`, and `help`.

## Spring Boot Intermediate Interview Questions

### 12. What are the basic Spring Boot annotations?
Some commonly used Spring Boot annotations are `@SpringBootApplication`, `@Configuration`, `@Component`, `@RestController`, and `@RequestMapping`. These annotations are used for bootstrapping the application, defining configuration, marking beans, building REST controllers, and mapping HTTP requests. They form the basic building blocks of a typical Spring Boot application.

### 13. What is Spring Boot dependency management?
Spring Boot dependency management makes it easier to handle project dependencies. It ensures that the libraries we add are compatible with the Spring Boot version we are using. This reduces version conflicts and avoids the need to manually manage every dependency version in the project.

### 14. Is it possible to change the port of the embedded Tomcat server in Spring Boot?
Yes, it is possible. By default, Spring Boot runs on port `8080`, but we can change it by setting the `server.port` property in `application.properties` or `application.yml`. For example, if we set `server.port=8081`, the application will run on port 8081.

### 15. What is the starter dependency of the Spring Boot module?
Spring Boot starters are pre-configured Maven or Gradle dependencies used for specific types of applications. For example, `spring-boot-starter-web` is used for web applications, `spring-boot-starter-data-jpa` is used for database access, and `spring-boot-starter-security` is used for security. These starters reduce manual dependency setup.

### 16. What is the default port of Tomcat in Spring Boot?
The default port of the embedded Tomcat server in Spring Boot is `8080`. If needed, we can change it using the `server.port` property in the application configuration file.

### 17. Can we disable the default web server in a Spring Boot application?
Yes, we can disable the default embedded web server if the application is not meant to run as a web application. One simple way mentioned often is setting `server.port=-1`, but in practice it is also common to configure the application as a non-web application depending on the use case.

### 18. How do you disable a specific auto-configuration class?
We can disable a specific auto-configuration class using the `exclude` attribute in `@EnableAutoConfiguration` or `@SpringBootApplication`. This is useful when Spring Boot is auto-configuring something that we do not want and we need more control over the configuration.

### 19. Can we create a non-web application in Spring Boot?
Yes, Spring Boot can be used for non-web applications as well. It is not limited to web development. We can use it to create console applications, batch processing applications, scheduled jobs, and microservices.

### 20. Describe the flow of HTTPS requests through a Spring Boot application.
When a client sends an HTTPS request, it first reaches the embedded server, such as Tomcat. Then Spring routes the request to the correct controller based on the URL mapping. The controller may call the service layer for business logic, and the service layer may interact with the repository layer for database operations. Finally, the response is returned to the client, usually as JSON in REST APIs or as a view in web applications.

### 21. Explain `@RestController` annotation in Spring Boot.
`@RestController` is used to create REST APIs in Spring Boot. It is a combination of `@Controller` and `@ResponseBody`. That means the methods in that class return data directly as the HTTP response body, usually in JSON format, instead of returning a view page.

### 22. Difference between `@Controller` and `@RestController`.
`@Controller` is mainly used in traditional web applications where the method returns a view, such as an HTML page. `@RestController` is mainly used for REST APIs, where the method returns data directly in the response body. So both handle requests, but `@RestController` is more suitable for API development.

### 23. What is the difference between `@RequestMapping` and `@GetMapping`?
`@RequestMapping` is a general-purpose annotation that can handle different HTTP methods like GET, POST, PUT, and DELETE. `@GetMapping` is a shortcut specifically for HTTP GET requests. So if the endpoint is only for GET, `@GetMapping` is simpler and more readable.

### 24. What is the difference between `@SpringBootApplication` and `@EnableAutoConfiguration`?
`@EnableAutoConfiguration` only enables Spring Boot’s automatic configuration feature. `@SpringBootApplication` is broader because it includes `@EnableAutoConfiguration` along with `@Configuration` and `@ComponentScan`. That is why `@SpringBootApplication` is usually used on the main class, while `@EnableAutoConfiguration` is used only when we want more specific control.

### 25. What are profiles in Spring?
Spring profiles are used to define environment-specific configurations, such as development, testing, and production. For example, we may want different database settings for different environments. Using profiles helps keep the configuration clean and makes it easy to switch behavior depending on where the application is running.

### 26. Mention the differences between WAR and embedded containers.
In the WAR approach, the application is packaged and deployed to an external application server. With embedded containers, the server, such as Tomcat, is packaged inside the application itself, usually in a JAR file. Embedded containers make deployment simpler, while WAR packaging is more common in traditional enterprise setups that use external servers.

## Spring Boot Interview Questions for Experienced

### 27. What is Spring Boot Actuator?
Spring Boot Actuator provides production-ready features for monitoring and managing an application. It gives useful endpoints for health, metrics, environment details, beans, and application status. It is very helpful in production for checking the condition of the application and troubleshooting issues.

### 28. How do you enable Actuator in a Spring Boot application?
To enable Actuator, we first add the `spring-boot-starter-actuator` dependency to the project. After that, we configure which actuator endpoints should be exposed in `application.properties` or `application.yml`. Once the application runs, those endpoints become available for monitoring and management.

### 29. What is the purpose of using `@ComponentScan`?
`@ComponentScan` tells Spring where to scan for components like `@Component`, `@Service`, `@Repository`, and `@Controller`. Once scanned, these classes are automatically registered as beans in the Spring container. This reduces manual bean configuration and helps Spring discover application components automatically.

### 30. What are `@RequestMapping` and `@RestController` used for in Spring Boot?
`@RequestMapping` is used to map incoming HTTP requests to controller methods based on URL, HTTP method, parameters, or headers. `@RestController` is used to define a controller whose methods return data directly instead of a view. Together, they are commonly used to build REST APIs in Spring Boot.

### 31. How do you get the list of all beans in your Spring Boot application?
We can get the list of all beans from the `ApplicationContext`. Since the Spring container manages all beans, the application context can provide their names and details. This is often useful for debugging, learning how the context is built, or verifying what beans are loaded.

### 32. Can we check environment properties in a Spring Boot application? How?
Yes, we can check environment properties using the `Environment` object provided by Spring. It allows us to read values from properties files, command-line arguments, and environment variables. This is useful when we want to access configuration values dynamically inside the application.

### 33. How do you enable debugging logs in a Spring Boot application?
We can enable debugging logs by configuring logging levels in `application.properties` or `application.yml`. For example, we can set a logger or package level to `DEBUG`. In some cases, actuator endpoints can also be used to change log levels at runtime, which is very helpful for troubleshooting production issues.

### 34. What is dependency injection and its types?
Dependency Injection is a design pattern where the required dependencies of a class are provided by the Spring container instead of the class creating them itself. This helps achieve loose coupling and makes the application easier to test and maintain. The main types are constructor injection, setter injection, and field injection, with constructor injection generally being the most preferred.

### 35. What is an IoC container?
The IoC container in Spring is the component responsible for creating, managing, and wiring beans. IoC stands for Inversion of Control, which means object creation and dependency management are handled by the framework rather than manually by the developer. This is one of the core ideas behind Spring.

### 36. What is the difference between constructor injection and setter injection?
In constructor injection, dependencies are provided at the time the object is created, which makes the object more complete and often more immutable. In setter injection, dependencies are set after object creation through setter methods, which gives more flexibility. Constructor injection is generally preferred for required dependencies, while setter injection is more suitable for optional ones.

## Bonus Spring Boot Interview Questions

### 37. What is Thymeleaf?
Thymeleaf is a server-side Java template engine used to create dynamic web pages. It is commonly used with Spring Boot in MVC applications. It allows us to bind backend data directly into HTML templates in a clean and readable way.

### 38. Explain Spring Data and what is Spring Data JPA.
Spring Data is a framework that simplifies data access in Spring applications. It provides common abstractions for working with different data sources. Spring Data JPA is a module of Spring Data that makes it easier to work with relational databases using JPA by reducing the amount of boilerplate repository code.

### 39. Explain Spring MVC.
Spring MVC is a web framework built on the Spring Framework and follows the Model-View-Controller design pattern. It is used to build web applications by separating request handling, business logic, and presentation. Controllers handle requests, models carry data, and views render the output.

### 40. What is a Spring Bean?
A Spring Bean is any object that is created, managed, and configured by the Spring IoC container. These beans form the core of a Spring application because Spring handles their lifecycle and dependency injection automatically.

### 41. What are inner beans in Spring?
An inner bean is a bean defined inside another bean’s configuration. It is usually used when the inner bean is needed only for one specific outer bean and does not need to be reused elsewhere. This helps keep the configuration more localized.

### 42. What is bean wiring?
Bean wiring is the process of connecting beans together inside the Spring container. In other words, it is how dependencies are injected from one bean into another. This can be done manually or automatically using autowiring.

### 43. What are Spring Boot DevTools used for?
Spring Boot DevTools are used to improve developer productivity during development. They provide features like automatic restart, faster reloads, and better development-time behavior. This helps developers test changes quickly without manually restarting the application every time.

### 44. What error do you see if H2 is not present in the classpath?
If the H2 database driver is missing from the classpath, we generally see an error like `java.lang.ClassNotFoundException: org.h2.Driver`. This happens because the application is trying to use the H2 driver, but the required class is not available.

### 45. Mention the steps to connect a Spring Boot application to a database using JDBC.
To connect a Spring Boot application to a database using JDBC, we first add the JDBC driver dependency for the required database. Then we configure the connection properties like URL, username, and password in the application configuration file. After that, we use classes like `JdbcTemplate` or related JDBC support to execute SQL queries.

### 46. What are the advantages of YAML over properties files, and how can YAML be loaded in Spring Boot?
YAML is often preferred because it is more readable and handles hierarchical configuration more naturally than `.properties` files. It is especially useful when the configuration is large or nested. In Spring Boot, YAML can be loaded automatically through `application.yml`, and it can also be bound using features like `@ConfigurationProperties`.

### 47. What do you understand about Spring Data REST?
Spring Data REST is a framework that automatically exposes Spring Data repositories as REST APIs. This reduces the amount of controller code we need to write. It is useful for quick development, but in real-world applications developers often prefer custom APIs for better control.

### 48. Why is Spring Data REST not recommended in real-world applications?
Spring Data REST is not always preferred in real-world applications because it gives less control over API design, versioning, security, and response structure. It can also be harder to handle complex business logic and entity relationships cleanly. That is why many teams prefer writing custom controllers and services.

### 49. How is Hibernate chosen as the default implementation for JPA without configuration?
When we add the `spring-boot-starter-data-jpa` dependency, Spring Boot automatically configures JPA support. Since Hibernate is included by default with that starter, Spring Boot uses it as the default JPA implementation unless we explicitly configure something else.

### 50. How do you deploy to a different server with Spring Boot?
To deploy a Spring Boot application to a different server, we first build the application as a JAR or WAR file. Then we move that package to the target server, make sure the required Java environment and configuration are available, and start the application there. If it is a JAR with an embedded server, deployment is usually simpler because the server is already packaged inside the application.
