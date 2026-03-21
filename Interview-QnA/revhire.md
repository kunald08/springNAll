# RevHire_P2 Interview Answers

## 174. Introduce yourself and explain your project.

My name is Kunal, and I worked on the RevHire project. RevHire is a full-stack recruitment web application built using Java, Spring Boot, Thymeleaf, Spring Security, Spring Data JPA, and Oracle XE in the current local setup. The project connects two types of users: job seekers and employers. Job seekers can register, build their profile, upload or create resumes, search for jobs, apply, and track application status. Employers can create company profiles, post jobs, review applicants, manage hiring actions, and monitor activity through a dashboard.

## 175. Explain your project to a client who has no technical knowledge.

RevHire is an online hiring platform. If you are a candidate, you can create your profile, upload your resume, search for jobs, and apply for them. If you are a company, you can post job openings, see who applied, shortlist or reject candidates, and track your hiring work in one place. So in simple terms, it works like a bridge between job seekers and employers.

## 176. What is your role in the project? Which module did you work on?

I mainly worked on the Job Seeker Profile and Resume module, and I also contributed to project setup, notification features, common configuration, and global exception handling. In the profile module, I handled profile CRUD, education, experience, skills, certifications, resume builder, resume upload, and profile viewing. I was also involved in setting up the Spring Boot project structure, database configuration, logging, and reusable layout fragments.

## 177. Explain the architecture and flow of your project.

RevHire follows a layered monolithic architecture. The browser sends a request to a Spring MVC controller. The controller accepts the request, validates data, and calls the service layer. The service layer contains the main business logic and communicates with the repository layer. The repository layer uses Spring Data JPA to interact with the database. After processing, the controller returns a Thymeleaf view, and the page is rendered on the server and sent back to the browser.

The main layers are:

- Presentation layer: Thymeleaf templates, controllers
- Business layer: services
- Data access layer: JPA repositories
- Database layer: Oracle XE in the current local configuration
- Cross-cutting layers: Spring Security, logging, validation, exception handling

## 178. Why did you use monolithic architecture instead of microservices?

We used monolithic architecture because this project is academic and workflow-driven, and we wanted faster development, simpler deployment, and easier debugging. Since all modules like authentication, profile, job posting, application tracking, and dashboard are closely connected, a monolith was a practical choice. Microservices would have added extra complexity like service communication, API gateway, distributed security, deployment coordination, and higher maintenance overhead, which was not necessary for this project stage.

## 179. Why did you create two separate projects — one for backend and one for MVC?

In the current `RevHire_P2` repository, we actually did not keep backend and MVC as two separate deployable projects. We implemented it as a single Spring Boot application using Spring MVC and Thymeleaf, so both backend logic and frontend rendering are inside the same codebase. If someone asks this in an interview, I would clarify that our final implementation is a monolithic Spring Boot MVC application, not a separate backend API plus separate MVC client.

## 180. Why didn't you use entity classes in the MVC project?

In our final implementation, that situation did not arise because we did not split the project into separate backend and MVC applications. We do use entity classes in this codebase, for example `User`, `Job`, `Application`, `Employer`, `JobSeekerProfile`, and `Notification`. If the project were split into two applications, then in the MVC or client layer I would prefer DTOs instead of exposing persistence entities directly, because DTOs reduce coupling and improve security and maintainability.

## 181. How does data flow through your project from frontend to database?

When a user submits a form from a Thymeleaf page, the request goes to a controller method using annotations like `@GetMapping` or `@PostMapping`. The form data is bound to a DTO or model object using `@ModelAttribute` or request parameters. The controller then calls the service layer. The service applies validations and business rules, creates or updates entity objects, and calls the repository. The repository uses JPA and Hibernate to save the data into the database. After that, the controller returns a success page or redirect along with model data for the UI.

For example, in the job application flow, the user opens the apply page, submits the application form, the `ApplicationController` receives it, `ApplicationServiceImpl` validates it, creates an `Application` entity, and `ApplicationRepository` stores it in the database.

## 182. How is the database connected in your project?

The database is connected using Spring Boot datasource configuration. In the current local setup, the active profile is `local`, and the actual database settings are in `application-local.properties`. The project uses Oracle XE with a JDBC URL, username, password, and Oracle driver class. Spring Data JPA and Hibernate are used to map entities to tables and perform CRUD operations.

## 183. How does the login page work? Explain the MVC flow and the `login.html` file.

The login page is served by `AuthController` through `GET /auth/login`, which returns the Thymeleaf template `auth/login`. In `login.html`, there is a login form whose action is `/login`, which is the Spring Security login processing URL. The form sends the email in the `username` field and the password in the `password` field.

After submit, Spring Security intercepts the request, and `CustomUserDetailsService` loads the user from the database using email. The password is checked using BCrypt. If authentication is successful, `CustomLoginSuccessHandler` redirects the user based on role. If the user is an employer, it sends them either to company profile creation or the employer dashboard. If the user is a seeker, it redirects to job search.

The same `login.html` page also supports OTP login. The user can switch from password login to OTP login, enter email, and request an OTP. That redirects to the OTP verification flow.

## 184. How is authentication implemented in your project?

Authentication is implemented using Spring Security with session-based login. We configured a custom security filter chain in `SecurityConfig`. For normal login, the user submits email and password, `CustomUserDetailsService` loads the user record, and `DaoAuthenticationProvider` uses `BCryptPasswordEncoder` to verify the password. On success, Spring Security creates an authenticated session.

We also added OTP-based flows for registration verification and login verification. During registration, OTP is generated and sent through email. Only after correct OTP verification is the user saved in the database. Similarly, OTP login and password reset are supported through email-based flows.

## 185. How is authorization / role-based access implemented?

Authorization is implemented using role-based access control in Spring Security. The `User` entity stores the role as an enum like `SEEKER`, `EMPLOYER`, or `ADMIN`. In `CustomUserDetailsService`, the role is converted into `SimpleGrantedAuthority`. Then in `SecurityConfig`, URL patterns are protected using `hasAuthority("SEEKER")` and `hasAuthority("EMPLOYER")`.

For example:

- `/applications/**`, `/profile/**`, `/resume/**`, `/favorites/**` are restricted to seekers
- `/employer/**`, `/employers/profile/**`, and employer job management routes are restricted to employers
- Public pages like login, register, home, and some job search pages are allowed without authentication

Some controller methods also use annotations like `@PreAuthorize`.

## 186. How are dashboards assigned according to user roles?

After successful login, `CustomLoginSuccessHandler` checks the user’s authority. If the role is `EMPLOYER`, the system checks whether the employer profile exists. If it does not exist, the user is redirected to create the company profile. If it exists, the user is redirected to `/employer/dashboard`. If the role is `SEEKER`, the user is redirected to `/jobs/search`. So the landing page and dashboard assignment are controlled dynamically after login based on role and profile state.

## 187. How is the frontend connected to the backend?

The frontend and backend are connected through Spring MVC inside the same application. The frontend is built using Thymeleaf templates, Bootstrap, CSS, and some JavaScript. The browser sends HTTP requests to controller endpoints. Controllers prepare model data and return view names. Thymeleaf then renders HTML on the server side and sends the final page to the browser. So instead of a separate frontend application calling REST APIs, this project uses server-side rendering.

## 188. How is data stored in the database?

Data is stored as relational tables mapped from JPA entities. For example:

- `users` stores common user account details
- `job_seeker_profiles` stores seeker-specific profile data
- `educations`, `experiences`, `skills`, `certifications`, and `resumes` store profile-related details
- `employers` stores company information
- `jobs` stores job postings
- `applications` stores job applications and their status
- `application_notes` stores internal employer notes
- `favorites` stores saved jobs
- `notifications` stores user notifications

Hibernate manages the mapping between entity classes and database tables.

## 189. How did you implement the admin module?

In the current codebase, there is an `ADMIN` role in the enum and the login success handler recognizes it, but a full separate admin module with controllers, screens, and management features is not fully implemented yet. The major completed roles are seeker and employer. If I explain this in an interview, I would answer honestly that admin support is defined at role level, but the main delivered business modules are for job seekers and employers.

## 190. How did you handle exceptions in your project?

We handled exceptions centrally using `@ControllerAdvice` in `GlobalExceptionHandler`. We created custom exceptions like `ResourceNotFoundException`, `BadRequestException`, `UnauthorizedException`, and `FileStorageException`. Based on the exception type, the handler returns the proper error view or redirect with a friendly message.

For example:

- resource not found returns a 404-style error page
- bad request returns a 400-style error page
- unauthorized access returns a 403-style error page
- file upload problems redirect back with an error message
- unexpected exceptions are logged and shown as a generic 500-style error page

This approach keeps controllers clean and provides consistent error handling.

## 191. What annotations did you use in your Spring application?

I used many Spring annotations depending on the layer. Some important ones are:

- `@SpringBootApplication` for the main class
- `@Controller`, `@Service`, `@Repository`, `@Component` for layer definitions
- `@Configuration`, `@Bean`, `@EnableWebSecurity` for configuration
- `@ControllerAdvice`, `@ExceptionHandler` for global exception handling
- `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`, `@Enumerated`, `@OneToMany`, `@ManyToOne`, `@OneToOne` for JPA entities
- `@GetMapping`, `@PostMapping`, `@RequestMapping`, `@PathVariable`, `@RequestParam`, `@ModelAttribute` for request handling
- `@Transactional` for transaction management
- `@Valid` for validation
- `@PreAuthorize` for method-level authorization
- `@Scheduled` for automatic background tasks like closing expired jobs

## 192. What DB tools did you use in your project?

In the codebase, I used Oracle XE as the active database, JDBC through Spring Boot datasource configuration, Spring Data JPA for repository access, and Hibernate as the ORM. For schema updates, we relied on `spring.jpa.hibernate.ddl-auto=update` during development. For query verification and database inspection, tools like SQL Developer or similar DB client tools can be used with Oracle XE.

If the interviewer refers to documentation, I can also mention that some earlier project material mentioned MySQL, but the current running local configuration in the repository uses Oracle XE.

## 193. How did you handle conflicts in your project (Git)?

We followed a branch-based workflow. Each team member worked on a feature branch like `feature/auth`, `feature/profile`, `feature/job`, `feature/application`, or `feature/dashboard`. Before pushing changes, we pulled the latest `develop` branch, resolved merge conflicts locally, tested again, and then created a pull request. While resolving conflicts, we carefully checked whether the issue was in controller logic, template changes, entity mappings, or common configuration files, because those were the most shared parts of the project.

## 194. If there are two Java files, how do you run them?

If they are plain standalone Java files, first I compile them with `javac`, and then I run the file that contains the `main` method using `java ClassName`. If one file is only a helper class and the other contains `main`, then I run only the main class.

In this project, since it is a Spring Boot application, I do not run individual Java files separately. I run the application starting from the main class `RevHireApplication` using Maven, for example with `mvn spring-boot:run`.

## 195. Show your project code and explain a specific module.

I would explain the Job Seeker Profile and Resume module because I worked on it directly. In this module, the seeker can create and update profile information like headline, summary, education, experience, skills, and certifications. I also implemented resume support in two ways: a structured resume builder and file upload for PDF or DOCX. The controller handles form submissions, the service layer applies business logic and validations, and the repository layer persists the data.

One good example is the resume flow. The seeker uploads a file from the UI, the controller receives the multipart request, the service validates file size and type, stores the file in the configured upload location, and saves the resume metadata in the database. Employers can later view the seeker profile and resume when reviewing applicants. This module is important because it supports the complete candidate side of the hiring journey before the application step.
