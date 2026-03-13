# WebSphere and Cloud Basics

## Table of Contents
1. [WebSphere Application Server](#1-websphere-application-server)
2. [WebSphere Architecture](#2-websphere-architecture)
3. [Application Deployment](#3-application-deployment)
4. [Cloud Basics](#4-cloud-basics)
5. [IaaS, PaaS, SaaS](#5-iaas-paas-saas)
6. [Cloud Providers](#6-cloud-providers)
7. [Cloud Services Overview](#7-cloud-services-overview)
8. [Simple Comparison](#8-simple-comparison)

---

## 1. WebSphere Application Server

WebSphere Application Server is an enterprise application server from IBM used to run Java-based web and business applications.

It provides an environment for applications to run reliably in enterprise systems.

### Why It Is Used

- host Java applications
- manage enterprise deployments
- support security and transactions
- support high availability

In simple words, WebSphere is a platform where enterprise Java applications run.

---

## 2. WebSphere Architecture

WebSphere architecture can be simple or distributed depending on company size.

### Basic Parts

- **Application Server**: runs the application
- **Deployment Manager**: manages configuration in larger environments
- **Node**: a machine or server that hosts application server instances
- **Cluster**: multiple servers working together for scalability and reliability

### Why Architecture Matters

Good architecture helps with:

- load distribution
- failover
- central management
- enterprise reliability

---

## 3. Application Deployment

Deployment means placing an application on a server so users can use it.

In Java enterprise systems, applications may be deployed as:

- WAR files
- EAR files

### Basic Deployment Steps

1. Build the application.
2. Create deployable package.
3. Upload or install package on server.
4. Configure required resources.
5. Start the application.
6. Test if it works correctly.

### Deployment Considerations

- environment variables
- database connection
- server ports
- security settings
- dependency versions

---

## 4. Cloud Basics

Cloud computing means using computing resources over the internet instead of owning and managing everything locally.

Examples of resources:

- servers
- storage
- databases
- networking
- software platforms

### Benefits

- scalable
- flexible
- pay for usage
- faster setup
- easier global access

---

## 5. IaaS, PaaS, SaaS

These are three common cloud service models.

### 5.1 IaaS

Infrastructure as a Service

Provider gives:

- virtual machines
- storage
- networking

You manage:

- OS
- runtime
- application

Example idea:

- renting a virtual server

### 5.2 PaaS

Platform as a Service

Provider gives:

- infrastructure
- OS
- runtime
- platform tools

You mainly manage:

- application code
- business logic

Example idea:

- deploy code without managing server setup

### 5.3 SaaS

Software as a Service

Provider gives the full software application.

You just use it.

Examples:

- Gmail
- Google Docs
- ServiceNow

---

## 6. Cloud Providers

### AWS

Amazon Web Services is one of the biggest cloud providers.

Known for many services in compute, storage, database, networking, and DevOps.

### Azure

Microsoft Azure is strong in enterprise and Microsoft ecosystem integration.

### GCP

Google Cloud Platform is known for data, analytics, and strong cloud infrastructure.

All three provide many similar core services, but with different naming and strengths.

---

## 7. Cloud Services Overview

Cloud providers offer many types of services, such as:

- virtual machines
- object storage
- managed databases
- load balancers
- serverless functions
- container services
- monitoring tools

### Example Usage

A company may:

- host application on cloud servers
- store files in cloud storage
- use managed database
- monitor performance using cloud tools

---

## 8. Simple Comparison

### Traditional On-Premise

- company buys hardware
- company manages server room
- setup is slower
- scaling is harder

### Cloud

- provider offers resources online
- setup is faster
- scaling is easier
- operating cost model is more flexible

---

## Final Summary

WebSphere is an enterprise application server used to run Java applications. Deployment is the process of making applications available on such servers.

Cloud computing provides infrastructure and software services over the internet. The three major models are:

- IaaS
- PaaS
- SaaS

Major cloud providers include AWS, Azure, and GCP.
