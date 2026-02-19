# Spring MVC & Thymeleaf - Complete In-Depth Guide

## Table of Contents
- [1. MVC Architecture](#1-mvc-architecture)
- [2. Spring Boot and Spring MVC Integration](#2-spring-boot-and-spring-mvc-integration)
- [3. Controllers](#3-controllers)
- [4. Request Mapping Annotations](#4-request-mapping-annotations)
- [5. @PathVariable and @RequestParam](#5-pathvariable-and-requestparam)
- [6. @RequestBody and @ResponseBody](#6-requestbody-and-responsebody)
- [7. Introduction to Thymeleaf](#7-introduction-to-thymeleaf)
- [8. Thymeleaf Configuration](#8-thymeleaf-configuration)
- [9. Thymeleaf Syntax and Expressions](#9-thymeleaf-syntax-and-expressions)
- [10. Thymeleaf Attributes](#10-thymeleaf-attributes)
- [11. Thymeleaf Layouts and Fragments](#11-thymeleaf-layouts-and-fragments)
- [12. Handling Form Submissions](#12-handling-form-submissions)

---

## 1. MVC Architecture

### What is MVC?

**MVC (Model-View-Controller)** is a design pattern that separates an application into three parts:

```
┌─────────┐     Request      ┌────────────────┐
│  User   │ ───────────────→ │  CONTROLLER    │
│(Browser)│                  │ (Handles input)│
└─────────┘                  └──────┬───┬─────┘
     ↑                              │   │
     │         ┌────────────────────┘   │
     │         ↓                        ↓
     │    ┌──────────┐          ┌───────────┐
     │    │  MODEL   │          │  VIEW     │
     │    │(Data +   │ ←──────→ │(HTML/UI)  │
     │    │ Logic)   │          │           │
     │    └──────────┘          └──────┬────┘
     │                                 │
     └─────────── Response ←───────────┘
```

### Three Components

| Component | Responsibility | Spring Annotation |
|-----------|---------------|-------------------|
| **Model** | Data + business logic | POJO / @Service / @Entity |
| **View** | What the user sees (HTML) | Thymeleaf templates |
| **Controller** | Handles requests, connects Model and View | @Controller |

### How It Works (Step by Step)

```
1. User enters URL: http://localhost:8080/users

2. CONTROLLER receives the request
   → @GetMapping("/users")

3. Controller asks SERVICE for data (Model)
   → userService.getAllUsers()

4. Controller adds data to the Model
   → model.addAttribute("users", userList)

5. Controller returns VIEW name
   → return "user-list"

6. VIEW ENGINE (Thymeleaf) renders HTML with data
   → Fills the template with user data

7. HTML response sent to browser
   → User sees the page
```

---

## 2. Spring Boot and Spring MVC Integration

### How Spring Boot Sets Up MVC

When you add `spring-boot-starter-web`, Spring Boot auto-configures:

```
✅ DispatcherServlet (front controller)
✅ Embedded Tomcat server (port 8080)
✅ View resolver for Thymeleaf (if starter added)
✅ JSON conversion with Jackson
✅ Static resource handling (/static, /public)
✅ Error handling (Whitelabel error page)
```

### Under the Hood: Request Flow

```
Browser sends: GET /users

1. Tomcat receives HTTP request
2. DispatcherServlet (Spring's front controller) intercepts it
3. DispatcherServlet asks HandlerMapping: "Who handles /users?"
4. HandlerMapping returns: UserController.getUsers()
5. DispatcherServlet calls the controller method
6. Controller returns model + view name
7. ViewResolver finds the template: templates/user-list.html
8. Thymeleaf renders HTML with model data
9. HTML sent back as HTTP response

┌──────────┐    ┌──────────────────┐    ┌──────────────────┐
│ Browser  │───→│ DispatcherServlet│───→│ HandlerMapping   │
│          │    │(Front Controller)│    │(Finds controller)│
└──────────┘    └────────┬─────────┘    └──────────────────┘
     ↑                   │
     │                   ↓
     │          ┌───────────────┐
     │          │  Controller   │ → calls Service → gets data
     │          └──────┬────────┘
     │                 │ returns view name + model
     │                 ↓
     │          ┌───────────────┐
     │          │ ViewResolver  │ → finds template
     │          └──────┬────────┘
     │                 ↓
     │          ┌──────────────┐
     │          │  Thymeleaf   │ → renders HTML
     │          └──────┬───────┘
     │                 │
     └────── HTML ←────┘
```

---

## 3. Controllers

### @Controller (For Web Pages — Returns HTML)

```java
@Controller  // Returns VIEW names (HTML pages)
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/users")
    public String getAllUsers(Model model) {
        List<User> users = userService.getAllUsers();
        model.addAttribute("users", users);    // Add data to model
        return "user-list";                     // Return VIEW name (template)
        // Thymeleaf looks for: templates/user-list.html
    }
    
    @GetMapping("/users/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.getUserById(id);
        model.addAttribute("user", user);
        return "user-detail";  // templates/user-detail.html
    }
}
```

### @RestController (For REST APIs — Returns JSON)

```java
@RestController  // Returns DATA directly (JSON) — NOT HTML
@RequestMapping("/api/users")
public class UserApiController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();  // Automatically converted to JSON
    }
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUserById(id);  // Returns JSON
    }
}
```

### @Controller vs @RestController

```java
// @Controller = @Component + returns VIEW names
@Controller
public class WebController {
    @GetMapping("/page")
    public String showPage() {
        return "page";  // Returns template name → Thymeleaf renders HTML
    }
}

// @RestController = @Controller + @ResponseBody
// Every method automatically returns data as JSON
@RestController
public class ApiController {
    @GetMapping("/data")
    public User getData() {
        return new User("Kunal");  // Returns JSON: {"name": "Kunal"}
    }
}

// @Controller with @ResponseBody = same as @RestController
@Controller
public class MixedController {
    
    @GetMapping("/page")
    public String showPage() {
        return "page";  // Returns HTML view
    }
    
    @GetMapping("/data")
    @ResponseBody  // This specific method returns JSON
    public User getData() {
        return new User("Kunal");  // Returns JSON
    }
}
```

---

## 4. Request Mapping Annotations

### @RequestMapping (Generic)

```java
@Controller
@RequestMapping("/users")  // Base path for all methods in this controller
public class UserController {
    
    @RequestMapping(method = RequestMethod.GET)
    public String list() { ... }
    
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public String detail(@PathVariable Long id) { ... }
    
    @RequestMapping(method = RequestMethod.POST)
    public String create() { ... }
}
```

### Shortcut Annotations (PREFERRED over @RequestMapping)

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping              // GET /api/users
    public List<User> getAll() { ... }
    
    @GetMapping("/{id}")     // GET /api/users/5
    public User getById(@PathVariable Long id) { ... }
    
    @PostMapping             // POST /api/users
    public User create(@RequestBody User user) { ... }
    
    @PutMapping("/{id}")     // PUT /api/users/5
    public User update(@PathVariable Long id, @RequestBody User user) { ... }
    
    @DeleteMapping("/{id}")  // DELETE /api/users/5
    public void delete(@PathVariable Long id) { ... }
    
    @PatchMapping("/{id}")   // PATCH /api/users/5
    public User partialUpdate(@PathVariable Long id, @RequestBody Map<String, Object> updates) { ... }
}
```

| Annotation | HTTP Method | Use Case |
|-----------|-------------|----------|
| `@GetMapping` | GET | Retrieve data |
| `@PostMapping` | POST | Create new resource |
| `@PutMapping` | PUT | Update entire resource |
| `@DeleteMapping` | DELETE | Delete resource |
| `@PatchMapping` | PATCH | Partial update |

---

## 5. @PathVariable and @RequestParam

### @PathVariable — Data in the URL Path

```java
// URL: /users/5         → id = 5
// URL: /users/42        → id = 42

@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}

// Multiple path variables
// URL: /users/5/orders/10
@GetMapping("/users/{userId}/orders/{orderId}")
public Order getOrder(@PathVariable Long userId, @PathVariable Long orderId) {
    return orderService.findByUserAndOrder(userId, orderId);
}

// Custom name
@GetMapping("/users/{user_id}")
public User getUser(@PathVariable("user_id") Long id) {
    return userService.findById(id);
}
```

### @RequestParam — Data in Query String

```java
// URL: /users?name=Kunal           → name = "Kunal"
// URL: /users?page=0&size=10       → page = 0, size = 10

@GetMapping("/users")
public List<User> searchUsers(@RequestParam String name) {
    return userService.findByName(name);
}

// Optional with default value
@GetMapping("/users")
public Page<User> listUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(required = false) String city  // Optional parameter
) {
    return userService.findAll(page, size, city);
}

// Multiple values
// URL: /users?ids=1,2,3
@GetMapping("/users")
public List<User> getUsersByIds(@RequestParam List<Long> ids) {
    return userService.findByIds(ids);
}
```

### @PathVariable vs @RequestParam

| Feature | @PathVariable | @RequestParam |
|---------|--------------|---------------|
| URL location | In the path | After `?` |
| Example | `/users/5` | `/users?id=5` |
| Required | Always (part of URL) | Can be optional |
| Use when | Identifying a specific resource | Filtering/searching |
| REST convention | Resource ID | Filters, pagination |

---

## 6. @RequestBody and @ResponseBody

### @RequestBody — Receive JSON from Client

```java
// Client sends (POST request with JSON body):
// {
//     "name": "Kunal",
//     "email": "kunal@email.com",
//     "age": 25
// }

@PostMapping("/users")
public User createUser(@RequestBody User user) {
    // Spring automatically converts JSON → Java object
    // user.getName() = "Kunal"
    // user.getEmail() = "kunal@email.com"
    return userService.save(user);
}
```

### @ResponseBody — Send JSON to Client

```java
@Controller
public class UserController {
    
    @GetMapping("/api/users/{id}")
    @ResponseBody  // Converts User object → JSON automatically
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
        // Returns JSON: {"id": 1, "name": "Kunal", "email": "kunal@email.com"}
    }
}

// With @RestController, @ResponseBody is automatic:
@RestController
public class UserController {
    @GetMapping("/api/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);  // Automatically JSON
    }
}
```

### Under the Hood: JSON Conversion

```
How does Spring convert Java ↔ JSON?

It uses HttpMessageConverter (specifically MappingJackson2HttpMessageConverter)

@RequestBody:  JSON string → Jackson ObjectMapper → Java object
@ResponseBody: Java object → Jackson ObjectMapper → JSON string

Jackson uses:
  - Getter methods → JSON field names (getName() → "name")
  - Setter methods → populate fields from JSON
  - Or @JsonProperty annotations
```

---

## 7. Introduction to Thymeleaf

### What is Thymeleaf?

Thymeleaf is a **server-side template engine** that generates HTML pages with dynamic data. It's like a smarter HTML file that can display Java data.

### How It Works

```
1. Controller adds data to Model
   → model.addAttribute("name", "Kunal")

2. Thymeleaf template uses the data
   → <p th:text="${name}">Default Name</p>

3. Thymeleaf renders HTML
   → <p>Kunal</p>

4. Browser receives pure HTML (no Thymeleaf syntax visible)
```

### Why Thymeleaf?

- **Natural templates** — Templates are valid HTML even without a server
- **Easy integration** with Spring Boot
- **No special IDE needed** — works with any HTML editor
- **Powerful features** — loops, conditions, fragments, layouts

---

## 8. Thymeleaf Configuration

### Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### Template Location

```
src/main/resources/
├── templates/           ← Thymeleaf templates go here
│   ├── index.html
│   ├── user-list.html
│   └── user-form.html
└── static/              ← Static files (CSS, JS, images)
    ├── css/
    │   └── style.css
    ├── js/
    │   └── app.js
    └── images/
        └── logo.png
```

### Default Configuration (Spring Boot auto-configures)

```properties
# These are the DEFAULTS — you don't need to set them
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.cache=true  # Set to false during development

# For development (auto-reload templates)
spring.thymeleaf.cache=false
```

---

## 9. Thymeleaf Syntax and Expressions

### Basic Template

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">  <!-- Enable Thymeleaf -->
<head>
    <title th:text="${pageTitle}">Default Title</title>
    <link rel="stylesheet" th:href="@{/css/style.css}">
</head>
<body>
    <h1 th:text="${message}">Default Message</h1>
    <p>Welcome, <span th:text="${user.name}">Guest</span>!</p>
</body>
</html>
```

### Expression Types

```
1. Variable Expression: ${...}
   → Access model attributes
   → ${user.name}, ${users.size()}, ${#dates.format(date)}

2. Selection Expression: *{...}
   → Access fields of selected object (with th:object)
   → *{name}, *{email}

3. Link Expression: @{...}
   → Build URLs
   → @{/users}, @{/users/{id}(id=${user.id})}

4. Message Expression: #{...}
   → Internationalization (i18n)
   → #{welcome.message}

5. Fragment Expression: ~{...}
   → Include template fragments
   → ~{fragments/header :: header}
```

### Variable Expressions: ${...}

```html
<!-- Controller: model.addAttribute("user", user) -->

<!-- Simple property -->
<p th:text="${user.name}">John</p>

<!-- Nested property -->
<p th:text="${user.address.city}">Unknown</p>

<!-- Method call -->
<p th:text="${user.getName()}">Unknown</p>

<!-- String concatenation -->
<p th:text="'Hello, ' + ${user.name} + '!'">Hello!</p>

<!-- Literal substitution (cleaner) -->
<p th:text="|Hello, ${user.name}!|">Hello!</p>

<!-- Conditional (ternary) -->
<p th:text="${user.active} ? 'Active' : 'Inactive'">Status</p>

<!-- Null safety (Elvis operator) -->
<p th:text="${user.nickname} ?: 'No nickname'">Nick</p>

<!-- Utility objects -->
<p th:text="${#strings.toUpperCase(user.name)}">NAME</p>
<p th:text="${#lists.size(users)}">0</p>
<p th:text="${#dates.format(user.createdAt, 'dd/MM/yyyy')}">Date</p>
```

### Link Expressions: @{...}

```html
<!-- Simple URL -->
<a th:href="@{/users}">All Users</a>
<!-- Output: <a href="/users">All Users</a> -->

<!-- URL with path variable -->
<a th:href="@{/users/{id}(id=${user.id})}">View User</a>
<!-- Output: <a href="/users/5">View User</a> -->

<!-- URL with query parameters -->
<a th:href="@{/users(page=${currentPage}, size=10)}">Next Page</a>
<!-- Output: <a href="/users?page=2&size=10">Next Page</a> -->

<!-- Static resources -->
<link th:href="@{/css/style.css}" rel="stylesheet">
<script th:src="@{/js/app.js}"></script>
<img th:src="@{/images/logo.png}" alt="Logo">
```

---

## 10. Thymeleaf Attributes

### th:text — Set Text Content

```html
<!-- Replaces the text content -->
<p th:text="${message}">This default text is replaced</p>

<!-- Unescaped HTML (be careful — XSS risk!) -->
<p th:utext="${htmlContent}">This will render HTML tags</p>
```

### th:each — Loop (For Each)

```html
<!-- Controller: model.addAttribute("users", userList) -->

<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
        </tr>
    </thead>
    <tbody>
        <!-- Loop through each user in the list -->
        <tr th:each="user : ${users}">
            <td th:text="${user.id}">1</td>
            <td th:text="${user.name}">John</td>
            <td th:text="${user.email}">john@email.com</td>
        </tr>
    </tbody>
</table>

<!-- With loop status variable -->
<tr th:each="user, iterStat : ${users}">
    <td th:text="${iterStat.index}">0</td>        <!-- 0-based index -->
    <td th:text="${iterStat.count}">1</td>         <!-- 1-based count -->
    <td th:text="${iterStat.size}">10</td>         <!-- Total size -->
    <td th:text="${iterStat.even}">true</td>       <!-- Is even row? -->
    <td th:text="${iterStat.odd}">false</td>       <!-- Is odd row? -->
    <td th:text="${iterStat.first}">true</td>      <!-- Is first? -->
    <td th:text="${iterStat.last}">false</td>      <!-- Is last? -->
    <td th:text="${user.name}">Name</td>
</tr>
```

### th:if and th:unless — Conditionals

```html
<!-- Show element ONLY if condition is true -->
<div th:if="${user.active}">
    <p>This user is active</p>
</div>

<!-- Show element ONLY if condition is false -->
<div th:unless="${user.active}">
    <p>This user is inactive</p>
</div>

<!-- Check for null -->
<p th:if="${user.email != null}" th:text="${user.email}">Email</p>
<p th:if="${user.email == null}">No email provided</p>

<!-- Check list is not empty -->
<div th:if="${not #lists.isEmpty(users)}">
    <p>Found users!</p>
</div>

<!-- th:switch / th:case -->
<div th:switch="${user.role}">
    <p th:case="'ADMIN'">Administrator</p>
    <p th:case="'USER'">Regular User</p>
    <p th:case="*">Unknown Role</p>  <!-- default case -->
</div>
```

### th:class and th:style — Dynamic Styling

```html
<!-- Dynamic class -->
<tr th:class="${user.active} ? 'active-row' : 'inactive-row'">...</tr>

<!-- Append class -->
<div class="base-class" th:classappend="${error} ? 'has-error'">Content</div>

<!-- Dynamic style -->
<p th:style="'color: ' + ${user.active ? 'green' : 'red'}">Status</p>
```

### th:attr — Set Any Attribute

```html
<input type="text" th:attr="placeholder=${placeholder}, maxlength=${maxLen}">

<!-- Specific attributes -->
<input th:value="${user.name}">
<input th:placeholder="${hint}">
<img th:src="${imageUrl}" th:alt="${imageAlt}">
<a th:href="@{/users/{id}(id=${user.id})}">Link</a>
```

---

## 11. Thymeleaf Layouts and Fragments

### What are Fragments?

Fragments are **reusable pieces of HTML** that you can include in multiple pages (like a header, footer, or navbar).

### Defining Fragments

```html
<!-- templates/fragments/common.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<!-- Header fragment -->
<head th:fragment="head(title)">
    <meta charset="UTF-8">
    <title th:text="${title}">Default Title</title>
    <link rel="stylesheet" th:href="@{/css/style.css}">
</head>

<!-- Navbar fragment -->
<nav th:fragment="navbar">
    <div class="navbar">
        <a th:href="@{/}">Home</a>
        <a th:href="@{/users}">Users</a>
        <a th:href="@{/about}">About</a>
    </div>
</nav>

<!-- Footer fragment -->
<footer th:fragment="footer">
    <div class="footer">
        <p>&copy; 2026 My Application. All rights reserved.</p>
    </div>
</footer>

</html>
```

### Using Fragments

```html
<!-- templates/user-list.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<!-- Include head fragment with parameter -->
<head th:replace="~{fragments/common :: head('User List')}">
</head>

<body>
    <!-- Include navbar -->
    <div th:replace="~{fragments/common :: navbar}"></div>
    
    <!-- Page content -->
    <h1>User List</h1>
    <table>
        <tr th:each="user : ${users}">
            <td th:text="${user.name}">Name</td>
        </tr>
    </table>
    
    <!-- Include footer -->
    <div th:replace="~{fragments/common :: footer}"></div>
</body>
</html>
```

### th:insert vs th:replace vs th:include

```html
<!-- Given fragment: -->
<footer th:fragment="footer">
    <p>Footer content</p>
</footer>

<!-- th:insert — inserts fragment INSIDE the host tag -->
<div th:insert="~{fragments/common :: footer}">
</div>
<!-- Result: <div><footer><p>Footer content</p></footer></div> -->

<!-- th:replace — REPLACES the host tag with the fragment -->
<div th:replace="~{fragments/common :: footer}">
</div>
<!-- Result: <footer><p>Footer content</p></footer> -->
```

### Layout Template (Decorator Pattern)

```html
<!-- templates/layout/base.html — The master layout -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:text="${pageTitle ?: 'My App'}">My App</title>
    <link rel="stylesheet" th:href="@{/css/style.css}">
</head>
<body>
    <nav th:replace="~{fragments/common :: navbar}"></nav>
    
    <main>
        <!-- This is where each page puts its content -->
        <div th:replace="${content}">
            Page content goes here
        </div>
    </main>
    
    <footer th:replace="~{fragments/common :: footer}"></footer>
</body>
</html>
```

---

## 12. Handling Form Submissions

### Complete Form Example

#### Step 1: The Entity/DTO

```java
public class UserForm {
    private Long id;
    private String name;
    private String email;
    private int age;
    private String city;
    
    // Constructors, getters, setters...
    public UserForm() {}
}
```

#### Step 2: The Controller

```java
@Controller
@RequestMapping("/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    // Show the form (GET)
    @GetMapping("/new")
    public String showCreateForm(Model model) {
        model.addAttribute("userForm", new UserForm());  // Empty form object
        model.addAttribute("cities", List.of("Mumbai", "Delhi", "Bangalore", "Chennai"));
        return "user-form";  // templates/user-form.html
    }
    
    // Process the form (POST)
    @PostMapping("/save")
    public String saveUser(@ModelAttribute("userForm") UserForm userForm,
                           BindingResult result,
                           RedirectAttributes redirectAttributes) {
        
        if (result.hasErrors()) {
            return "user-form";  // Show form again with errors
        }
        
        userService.save(userForm);
        redirectAttributes.addFlashAttribute("success", "User created successfully!");
        return "redirect:/users";  // Redirect to user list (PRG pattern)
    }
    
    // Show edit form (GET)
    @GetMapping("/edit/{id}")
    public String showEditForm(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("userForm", user);
        model.addAttribute("cities", List.of("Mumbai", "Delhi", "Bangalore", "Chennai"));
        return "user-form";
    }
    
    // List all users (GET)
    @GetMapping
    public String listUsers(Model model) {
        model.addAttribute("users", userService.findAll());
        return "user-list";
    }
    
    // Delete user
    @GetMapping("/delete/{id}")
    public String deleteUser(@PathVariable Long id, RedirectAttributes redirectAttributes) {
        userService.deleteById(id);
        redirectAttributes.addFlashAttribute("success", "User deleted!");
        return "redirect:/users";
    }
}
```

#### Step 3: The Form Template

```html
<!-- templates/user-form.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>User Form</title>
</head>
<body>
    <h1 th:text="${userForm.id != null} ? 'Edit User' : 'Create User'">User Form</h1>
    
    <!-- Form bound to userForm object -->
    <form th:action="@{/users/save}" th:object="${userForm}" method="post">
        
        <!-- Hidden ID field for editing -->
        <input type="hidden" th:field="*{id}">
        
        <!-- Name field -->
        <div>
            <label for="name">Name:</label>
            <input type="text" th:field="*{name}" id="name" placeholder="Enter name">
            <!-- th:field="*{name}" → generates: name="name" value="Kunal" -->
        </div>
        
        <!-- Email field -->
        <div>
            <label for="email">Email:</label>
            <input type="email" th:field="*{email}" id="email" placeholder="Enter email">
        </div>
        
        <!-- Age field -->
        <div>
            <label for="age">Age:</label>
            <input type="number" th:field="*{age}" id="age" min="1" max="120">
        </div>
        
        <!-- City dropdown -->
        <div>
            <label for="city">City:</label>
            <select th:field="*{city}" id="city">
                <option value="">-- Select City --</option>
                <option th:each="city : ${cities}" 
                        th:value="${city}" 
                        th:text="${city}">City</option>
            </select>
        </div>
        
        <!-- Submit button -->
        <button type="submit">Save</button>
        <a th:href="@{/users}">Cancel</a>
    </form>
</body>
</html>
```

#### Step 4: The List Template

```html
<!-- templates/user-list.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Users</title>
</head>
<body>
    <h1>User List</h1>
    
    <!-- Success message -->
    <div th:if="${success}" style="color: green;">
        <p th:text="${success}">Success message</p>
    </div>
    
    <a th:href="@{/users/new}">+ Add New User</a>
    
    <table border="1">
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Email</th>
                <th>Age</th>
                <th>City</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr th:each="user : ${users}">
                <td th:text="${user.id}">1</td>
                <td th:text="${user.name}">Name</td>
                <td th:text="${user.email}">Email</td>
                <td th:text="${user.age}">0</td>
                <td th:text="${user.city}">City</td>
                <td>
                    <a th:href="@{/users/edit/{id}(id=${user.id})}">Edit</a>
                    <a th:href="@{/users/delete/{id}(id=${user.id})}" 
                       onclick="return confirm('Are you sure?')">Delete</a>
                </td>
            </tr>
        </tbody>
    </table>
    
    <p th:if="${#lists.isEmpty(users)}">No users found.</p>
</body>
</html>
```

### How th:field Works Under the Hood

```html
<!-- th:field="*{name}" on an input generates THREE things: -->
<input type="text" th:field="*{name}">

<!-- Becomes: -->
<input type="text" id="name" name="name" value="Kunal">
<!--                 ^^^^^^  ^^^^^^^^^^  ^^^^^^^^^^^^^^ -->
<!--                  id      form param   current value  -->

<!-- When form is submitted: -->
<!-- POST data: name=Kunal&email=kunal@email.com&age=25 -->
<!-- Spring binds these to the userForm object automatically! -->
```

---

## Quick Revision Cheat Sheet

```
MVC Pattern:
  Model = Data (entities, DTOs)
  View = HTML pages (Thymeleaf templates)
  Controller = Handles requests, connects Model ↔ View

Controllers:
  @Controller → Returns view names (HTML pages)
  @RestController → Returns data (JSON)

Mappings:
  @GetMapping → GET requests (read)
  @PostMapping → POST requests (create)
  @PutMapping → PUT requests (update)
  @DeleteMapping → DELETE requests (delete)

Parameters:
  @PathVariable → /users/{id} → id = 5
  @RequestParam → /users?name=Kunal → name = "Kunal"
  @RequestBody → JSON body → Java object
  @ResponseBody → Java object → JSON response

Thymeleaf Expressions:
  ${variable}     → Access model attribute
  *{field}        → Access field of th:object
  @{/url}         → Build URL
  #{message}      → i18n message

Thymeleaf Attributes:
  th:text          → Set text content
  th:each          → Loop
  th:if / th:unless → Conditional
  th:href          → Set href
  th:field         → Bind form field
  th:object        → Set form object

Forms:
  th:action="@{/save}" → Form submission URL
  th:object="${form}" → Bind form to object
  th:field="*{name}" → Bind field to property
  @ModelAttribute → Receive form data in controller

Fragments:
  th:fragment="name" → Define reusable piece
  th:replace="~{file :: fragment}" → Include fragment
```

---

**Next: [09-REST-APIs.md](09-REST-APIs.md) — REST Principles, HTTP Methods, Status Codes, and ResponseEntity**
