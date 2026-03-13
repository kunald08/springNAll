# JIRA Basics

## Table of Contents
1. [Introduction to JIRA](#1-introduction-to-jira)
2. [Getting Started with JIRA](#2-getting-started-with-jira)
3. [Working with Issues in JIRA](#3-working-with-issues-in-jira)
4. [JIRA Workflows](#4-jira-workflows)
5. [JIRA Administration](#5-jira-administration)
6. [Agile Boards](#6-agile-boards)
7. [Reporting and Dashboards](#7-reporting-and-dashboards)
8. [Simple Team Example](#8-simple-team-example)

---

## 1. Introduction to JIRA

JIRA is a project and issue tracking tool. It is widely used by software teams to plan work, assign tasks, track bugs, and monitor progress.

Teams use JIRA for:

- bug tracking
- task management
- sprint planning
- release tracking
- reporting

In simple words, JIRA helps a team know:

- what work exists
- who is doing it
- what stage it is in
- what is blocked

---

## 2. Getting Started with JIRA

### Main Terms

#### Project

A project is a workspace for related work items.

Examples:

- Banking App
- Employee Portal
- Inventory System

#### Issue

An issue is a single work item in JIRA.

Examples:

- bug
- task
- story
- epic

#### User

A user is a team member who can create, update, or manage work.

#### Board

A board is a visual view of issues, often shown as columns like:

- To Do
- In Progress
- Done

---

## 3. Working with Issues in JIRA

An issue is the heart of JIRA.

### Common Issue Types

- **Bug**: something is broken
- **Task**: a piece of work to complete
- **Story**: a user requirement
- **Epic**: a large feature made of smaller stories

### Common Issue Fields

- summary
- description
- assignee
- priority
- status
- labels
- attachment
- due date

### Typical Issue Life Cycle

1. Create issue
2. Assign issue
3. Work on issue
4. Test issue
5. Close issue

### Example

Issue summary:

`Login page does not show error for wrong password`

This issue may include:

- steps to reproduce
- expected result
- actual result
- screenshots
- priority

Good issues are clear and complete.

---

## 4. JIRA Workflows

A workflow defines how an issue moves from one status to another.

Example workflow:

`To Do -> In Progress -> In Review -> Testing -> Done`

### Why Workflows Matter

Workflows help teams:

- follow a standard process
- avoid confusion
- track progress clearly
- control approvals

### Transition

A transition is the movement from one status to another.

Example:

- `In Progress` to `Testing`

Rules can also be added. For example:

- only testers can move to `Done`
- resolution must be set before closing

---

## 5. JIRA Administration

JIRA administrators manage the tool setup.

Their work may include:

- creating projects
- managing users and roles
- configuring issue types
- defining workflows
- setting permissions
- creating custom fields

### Permissions

Permissions decide what users can do.

Examples:

- create issues
- edit issues
- assign issues
- close issues
- administer project

Proper permissions are important for control and security.

---

## 6. Agile Boards

JIRA supports agile working, especially **Scrum** and **Kanban**.

### 6.1 Scrum Board

Used when work is done in fixed time periods called sprints.

Important Scrum ideas:

- product backlog
- sprint backlog
- sprint planning
- daily standup
- sprint review
- retrospective

A Scrum board helps track sprint work.

### 6.2 Kanban Board

Used for continuous flow of work.

There may be no sprint. Work moves continuously across columns.

Kanban focuses on:

- visualizing work
- limiting work in progress
- improving flow

### Difference

- Scrum is sprint-based
- Kanban is continuous-flow based

---

## 7. Reporting and Dashboards

JIRA provides reports and dashboards so teams can see project health.

### Common Reports

- burndown chart
- sprint report
- velocity chart
- created vs resolved chart
- pie chart by issue type

### Dashboard

A dashboard is a page that shows selected reports or widgets.

It helps managers and teams quickly understand:

- pending work
- completed work
- team progress
- blockers

---

## 8. Simple Team Example

Suppose a team is building an e-commerce app.

They may use JIRA like this:

- create an epic for `Payment Module`
- add stories for payment screens
- create bugs when errors are found
- assign tasks to developers
- move issues through workflow
- use Scrum board in each sprint
- check reports after sprint end

This keeps the whole team aligned.

---

## Final Summary

JIRA is a tool for tracking work and managing projects. The main building block is the issue. Workflows show how work moves, boards show current progress, and dashboards show overall status.

Important areas to remember:

- issues
- workflows
- permissions
- Scrum and Kanban boards
- reports and dashboards
