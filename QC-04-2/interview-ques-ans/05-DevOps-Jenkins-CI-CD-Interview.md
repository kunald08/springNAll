# DevOps, Jenkins, CI/CD — Interview Questions & Answers

---

## 1. What is DevOps?

**Answer:**
DevOps is a culture and working approach that improves collaboration between development and operations teams. Its goal is to deliver software faster, more reliably, and with better automation across build, test, deployment, and monitoring.

---

## 2. What problem does DevOps solve?

**Answer:**
DevOps solves the gap between development and operations. In traditional models, developers build software and operations deploy it separately, which can cause delays, miscommunication, and unstable releases. DevOps reduces these issues through collaboration and automation.

---

## 3. What is Continuous Integration?

**Answer:**
Continuous Integration, or CI, is the practice of frequently merging code into a shared repository and automatically running checks such as build and tests. The purpose is to find integration issues early and keep the codebase stable.

---

## 4. What is Continuous Delivery?

**Answer:**
Continuous Delivery means the application is always kept in a deployable state. After successful build and test stages, the software is ready for release, but a manual approval may still be needed before deploying to production.

---

## 5. What is Continuous Deployment?

**Answer:**
Continuous Deployment is a step beyond Continuous Delivery. In this approach, if all pipeline checks pass, the software is automatically deployed to production without manual intervention. It requires high confidence in automation and testing.

---

## 6. What is Jenkins?

**Answer:**
Jenkins is an automation server used to implement CI/CD. It can pull code from a repository, build the project, run automated tests, create deployable artifacts, and trigger deployment steps. It is widely used because it is flexible and supports many plugins.

---

## 7. What is a Jenkins job?

**Answer:**
A Jenkins job is a configured task that Jenkins executes. For example, a job may compile a Java project, run tests, package the application, or deploy it to an environment.

---

## 8. What is a Jenkins pipeline?

**Answer:**
A Jenkins pipeline is a sequence of automated stages such as checkout, build, test, package, and deploy. It represents the end-to-end CI/CD workflow and helps standardize release processes.

---

## 9. What are the benefits of CI/CD?

**Answer:**
The major benefits are:

- faster delivery
- early detection of issues
- less manual effort
- more reliable deployments
- quick feedback for developers

In short, CI/CD improves both speed and quality.

---

## 10. Can you describe a simple Jenkins-based CI/CD flow?

**Answer:**
Yes. A developer pushes code to Git, Jenkins detects the change, checks out the code, builds the application, runs tests, creates an artifact such as a JAR or WAR, and then deploys it to a target environment. If any step fails, Jenkins marks the build failed and notifies the team.
