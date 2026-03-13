# DevOps, Jenkins, CI, CD

## Table of Contents
1. [DevOps Overview](#1-devops-overview)
2. [What Is Continuous Integration?](#2-what-is-continuous-integration)
3. [What Is Continuous Delivery?](#3-what-is-continuous-delivery)
4. [What Is Continuous Deployment?](#4-what-is-continuous-deployment)
5. [Jenkins Overview](#5-jenkins-overview)
6. [Jenkins Job or Project](#6-jenkins-job-or-project)
7. [Typical CI/CD Pipeline](#7-typical-cicd-pipeline)
8. [Simple Example](#8-simple-example)
9. [Best Practices](#9-best-practices)

---

## 1. DevOps Overview

DevOps is a way of working where development and operations teams collaborate closely to build, test, release, and maintain software faster and more reliably.

### Traditional Problem

In many companies:

- developers write code
- operations deploy and manage it

When they work separately, problems happen:

- slow releases
- blame culture
- deployment failures
- unclear ownership

### DevOps Goal

DevOps tries to improve:

- collaboration
- automation
- speed
- reliability
- continuous improvement

DevOps is not just a tool. It is a **culture + process + automation approach**.

---

## 2. What Is Continuous Integration?

**Continuous Integration (CI)** means developers regularly merge code into a shared repository, and automated checks run each time.

These checks may include:

- build
- compile
- unit test
- code quality checks

### Why CI Is Useful

- integration problems are found early
- broken code is detected quickly
- team collaboration becomes safer

Without CI, teams may discover integration problems very late.

---

## 3. What Is Continuous Delivery?

**Continuous Delivery** means the software is always kept in a deployable state.

After CI completes successfully:

- the application is built
- tested
- packaged
- prepared for release

But production release usually still needs a manual approval.

### Key Idea

The team can release anytime, because the system is always ready.

---

## 4. What Is Continuous Deployment?

**Continuous Deployment** goes one step further.

If all pipeline stages pass, the software is automatically released to production without manual approval.

### Difference

- **Continuous Delivery**: ready for release, but human approval may be needed
- **Continuous Deployment**: automatically released after passing checks

Continuous deployment requires strong testing and confidence.

---

## 5. Jenkins Overview

Jenkins is an automation server used to build, test, and deploy software.

It is one of the most popular CI/CD tools.

### What Jenkins Can Do

- pull code from Git
- build applications
- run automated tests
- trigger scripts
- deploy to servers
- schedule jobs
- integrate with many tools

Jenkins supports plugins, so it can connect with many systems.

---

## 6. Jenkins Job or Project

In Jenkins, a **job** or **project** is a configured task.

Examples:

- compile Java project
- run unit tests
- deploy WAR file
- back up database

### Common Job Types

- freestyle project
- pipeline project
- multibranch pipeline

### Pipeline

A pipeline describes the steps of build, test, and deploy.

This is usually the modern approach.

Example stages:

- checkout
- build
- test
- package
- deploy

---

## 7. Typical CI/CD Pipeline

Here is a simple pipeline flow:

1. Developer pushes code to Git repository.
2. Jenkins detects the change.
3. Jenkins checks out the latest code.
4. Build starts.
5. Automated tests run.
6. Artifact is created, such as a JAR or WAR.
7. Deployment happens to test or staging.
8. After approval or success, deployment may happen to production.

This pipeline reduces manual work and human error.

---

## 8. Simple Example

Imagine a Spring Boot project.

When a developer pushes code:

- Jenkins downloads the code
- runs Maven build
- runs tests
- creates JAR
- deploys to test environment

If any step fails, Jenkins marks the build failed and notifies the team.

This gives fast feedback.

---

## 9. Best Practices

- keep builds fast
- run tests automatically
- fix broken builds quickly
- store pipeline as code when possible
- automate repetitive steps
- use separate environments for test and production

---

## Final Summary

DevOps focuses on collaboration and automation. CI helps integrate code safely, Continuous Delivery keeps software release-ready, and Continuous Deployment automates release fully.

Jenkins is a major tool used to implement these ideas through jobs and pipelines.
