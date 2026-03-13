# ServiceNow Basics

## Table of Contents
1. [Introduction to ServiceNow](#1-introduction-to-servicenow)
2. [ServiceNow Architecture](#2-servicenow-architecture)
3. [Modules and Applications](#3-modules-and-applications)
4. [ServiceNow Administration](#4-servicenow-administration)
5. [ServiceNow ITSM](#5-servicenow-itsm)
6. [Simple Example](#6-simple-example)

---

## 1. Introduction to ServiceNow

ServiceNow is a cloud-based platform used by organizations to manage IT services, workflows, and business processes.

It is often used for:

- incident management
- request management
- change management
- asset management
- service desk operations

In simple words, ServiceNow helps teams manage work in an organized and trackable way.

---

## 2. ServiceNow Architecture

ServiceNow follows a cloud-based architecture.

### Main Idea

Users access the platform through a browser. The application runs on ServiceNow servers, and data is stored in the platform database.

### Basic Architecture Parts

- **User Interface**: what users see in browser
- **Application Layer**: business logic and workflows
- **Database Layer**: stores records such as incidents and users

### Multi-Instance Idea

Organizations usually get separate environments such as:

- development
- testing
- production

This helps teams test changes safely before going live.

---

## 3. Modules and Applications

ServiceNow is divided into applications and modules.

### Application

An application is a major functional area.

Examples:

- Incident
- Change
- Problem
- Knowledge
- Service Catalog

### Module

A module is a menu item inside an application.

For example, inside Incident application, modules may include:

- Create New
- Open
- Assigned to Me

### Records

A record is one stored item in a table.

Examples:

- one incident ticket
- one user
- one change request

ServiceNow uses tables heavily. Each module often works with records from one or more tables.

---

## 4. ServiceNow Administration

An administrator manages the platform setup and configuration.

Common admin tasks:

- create users and groups
- assign roles
- manage forms and fields
- configure notifications
- create workflows
- manage access control

### Roles

Roles define permissions.

Examples:

- admin
- itil
- approver

### Forms and Lists

- **Form**: detailed view of one record
- **List**: table view of many records

Admins often customize forms and lists to meet business needs.

---

## 5. ServiceNow ITSM

ITSM means **IT Service Management**.

ServiceNow is well known for ITSM support.

### Common ITSM Areas

#### Incident Management

Used when something is broken and needs quick restoration.

Example:

- email server is down

Goal:

- restore service as fast as possible

#### Problem Management

Used to find the root cause of repeated incidents.

Example:

- same login issue happens every week

Goal:

- remove the real underlying cause

#### Change Management

Used to control changes in systems.

Example:

- deploying a new application version

Goal:

- reduce risk while making changes

#### Request Management

Used when users ask for something standard.

Example:

- new laptop request
- software access request

Goal:

- fulfill requests smoothly

---

## 6. Simple Example

Suppose an employee cannot log in to a company portal.

In ServiceNow:

1. A service desk agent creates an incident.
2. The incident is assigned to a support team.
3. The support team investigates and resolves the issue.
4. If the issue keeps repeating, a problem record may be created.
5. If a system fix is required, a change request may be raised.

This shows how incident, problem, and change can be connected.

---

## Final Summary

ServiceNow is a cloud platform for managing IT services and workflows. It is built around applications, modules, records, roles, and process automation.

Important concepts:

- cloud-based architecture
- modules and applications
- admin configuration
- ITSM processes such as incident, problem, change, and request
