# Interview Answers - Plain English

## 196. What are Microservices? What are their advantages?

Microservices are an architecture where one big application is split into small independent services. Each service handles one specific business function, like authentication, payment, order, or notification.

The main advantages are:

- each service can be developed and deployed independently
- easier to scale only the required service
- better fault isolation
- different teams can work on different services
- easier to maintain large applications over time

## 197. What is the difference between Microservices and Monolithic architecture?

In monolithic architecture, the whole application is built and deployed as one single unit.

In microservices architecture, the application is divided into multiple small services.

So in monolith, all modules are tightly packaged together. In microservices, modules are separated and communicate through APIs or messaging.

## 198. What is an API Gateway? What does it do?

An API Gateway is the single entry point for client requests in a microservices system. Instead of the client calling many services directly, it calls the API Gateway.

The API Gateway can do things like:

- route requests to the correct service
- handle authentication and authorization
- rate limiting
- logging and monitoring
- request aggregation

## 199. How do microservices communicate with each other?

Microservices usually communicate in two ways:

- synchronous communication using REST APIs or HTTP
- asynchronous communication using message brokers like Kafka or RabbitMQ

If an immediate response is needed, REST is common. If loose coupling is needed, messaging is better.

## 200. What is CI/CD? How does it work?

CI/CD means Continuous Integration and Continuous Delivery or Deployment.

Continuous Integration means developers regularly push code, and the system automatically builds and tests it.

Continuous Delivery or Deployment means after that, the application is automatically prepared or deployed to the target environment.

So the flow is usually: code push, build, test, package, deploy.

## 201. What services does Cloud provide?

Cloud mainly provides services like:

- compute services
- storage services
- database services
- networking
- security
- monitoring
- serverless services
- analytics and AI services

## 202. Name some compute, storage, database, and serverless services on AWS/Azure.

On AWS:

- compute: EC2
- storage: S3
- database: RDS, DynamoDB
- serverless: Lambda

On Azure:

- compute: Virtual Machines
- storage: Blob Storage
- database: Azure SQL Database, Cosmos DB
- serverless: Azure Functions

## 203. Explain the Git flow: working directory → staging → local repo → remote repo.

First, I make changes in my working directory.

Then I add the required files to the staging area using `git add`.

After that, I commit the staged changes to the local repository using `git commit`.

Finally, I push those commits to the remote repository using `git push`.

So the flow is: working directory, then staging area, then local repo, then remote repo.

## 204. What is git checkout? How do you create a new branch?

`git checkout` is used to switch branches or restore files in older Git usage.

To create a new branch and switch to it, I can use:

```bash
git checkout -b feature-branch
```

In newer Git, I can also use:

```bash
git switch -c feature-branch
```

## 205. What is the staging area in Git?

The staging area is the place between working directory and commit. It is where I select which changes should go into the next commit.

## 206. How do you resolve merge conflicts in Git?

When a merge conflict happens, I open the conflicted file, check both versions carefully, and decide what the final correct code should be. Then I remove the conflict markers, save the file, add it again using `git add`, and complete the merge with a commit.

## 207. What are the common Git commands you have used?

Common Git commands I have used are:

- `git init`
- `git clone`
- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`
- `git branch`
- `git checkout`
- `git switch`
- `git merge`
- `git log`
- `git diff`

## 208. What is the difference between Unit Testing and Integration Testing?

Unit testing checks one small unit of code, like a method or a class, in isolation.

Integration testing checks whether multiple parts of the application work correctly together, like controller, service, repository, and database flow.

## 209. What is JUnit? What are the types of test annotations in JUnit?

JUnit is a Java testing framework used to write and run test cases.

Common JUnit annotations are:

- `@Test`
- `@BeforeEach`
- `@AfterEach`
- `@BeforeAll`
- `@AfterAll`
- `@Disabled`

If the project uses older JUnit, we may also see annotations like `@Before` and `@After`.

## 210. What is Mockito? How do you use @Mock and @InjectMocks?

Mockito is a framework used for mocking dependencies in unit tests.

`@Mock` is used to create a fake object for a dependency.

`@InjectMocks` is used to create the object under test and inject the mocked dependencies into it.

This helps us test one class without depending on the real database or external services.

## 211. Explain test cases you have written in your project.

In my project, I wrote test cases mainly for service layer logic. For example, I tested whether data is saved correctly, whether validation works, whether duplicate conditions are handled, and whether expected exceptions are thrown for invalid cases.

If I talk about RevHire, examples include testing profile operations, job-related logic, application flow, and service methods that depend on repositories.

## 212. What is event handling in JavaScript? Give an example.

Event handling means responding to user actions like click, key press, submit, or mouse move.

For example, when a user clicks a button, JavaScript can run some code.

```javascript
button.onclick = function() {
  alert("Button clicked");
};
```

## 213. What is addEventListener? Write an example.

`addEventListener` is used to attach an event handler to an element.

Example:

```javascript
document.getElementById("btn").addEventListener("click", function() {
  alert("Clicked");
});
```

## 214. What is the DOM? How do you manipulate it using JavaScript?

DOM stands for Document Object Model. It represents the HTML page as objects.

Using JavaScript, I can select elements, change text, change styles, add classes, create new elements, or remove elements.

For example, I can use `document.getElementById()` and then update `innerText` or `style`.

## 215. What is the difference between relative, fixed, and absolute positioning in CSS?

`relative` means the element is positioned relative to its normal position.

`absolute` means the element is positioned relative to its nearest positioned parent.

`fixed` means the element stays fixed on the screen even when the page scrolls.

## 216. What are the basic HTML and CSS concepts used in your project?

In my project, I used basic HTML concepts like forms, inputs, buttons, tables, links, divs, sections, and semantic tags.

In CSS, I used layout concepts like flexbox, spacing, colors, positioning, responsive design, and Bootstrap classes for styling.

## 217. Tell me about yourself. (2-minute self introduction)

My name is Kunal. I come from a technical background, and I have been focusing mainly on Java, Spring Boot, SQL, and web application development. During my training, I worked on backend and full-stack concepts, and I also completed project work where I implemented real business modules. My main strengths are problem solving, willingness to learn, and writing structured code. I enjoy working on Java-based applications because I like both the logic part and the design of scalable backend systems. Right now, I am looking for an opportunity where I can start as a Java developer, improve through real project exposure, and contribute well to the team.

## 218. Explain your project briefly.

My project was RevHire, which is a job portal application. It connects job seekers and employers. Job seekers can register, build profiles, upload resumes, search for jobs, and apply. Employers can create company profiles, post jobs, review applicants, and manage hiring flow. The project was built using Java, Spring Boot, Spring Security, JPA, Thymeleaf, and a relational database.

## 219. Why did you choose Java as your primary programming language?

I chose Java because it is strong in object-oriented programming, widely used in enterprise applications, and has a very strong ecosystem. It is also reliable, platform independent, and highly used in backend development. Since I want to build a career in backend and enterprise software, Java is a very good choice.

## 220. How do you rate yourself in Java? Justify your rating.

I would rate myself around 7.5 out of 10 in Java.

I am strong in core Java, OOP, collections, exception handling, JDBC basics, Spring Boot basics, and backend project development. At the same time, I know there is still a lot to learn in advanced topics like performance tuning, distributed systems, and deep JVM internals, so I prefer to give an honest rating.

## 221. What have you achieved during your training?

During my training, I built strong understanding in Java, Spring Boot, SQL, Git, and web development basics. I also worked on project implementation, improved my coding confidence, learned how to structure a real application, and got experience in debugging, testing, and version control.

## 222. Are you willing to relocate?

Yes, I am willing to relocate if the job requires it.

## 223. Are you willing to work in shifts?

Yes, I am comfortable working in shifts if required by the company or project.

## 224. Are you willing to learn new technologies if the project requires it?

Yes, definitely. I believe learning is part of the job in IT. If the project requires a new technology, I am ready to learn it and adapt quickly.

## 225. How would you handle a technology change in the company?

I would handle it positively. First, I would understand why the change is happening and what business value it brings. Then I would start learning the new technology step by step, use documentation and hands-on practice, and take support from team members if needed. I see technology change as an opportunity to grow.

## 226. Do you have any questions for us?

Yes, I would like to ask a few questions.

- What kind of projects or technologies will I be working on initially?
- What does the learning and growth path look like for a fresher in your company?
- How does the team support new members during onboarding?

These questions help me understand how I can contribute and grow in the role.
