---
created: 2024-05-29 12:44
author: Tomasz Olejarczuk
title: RabbitMQ Integration for Asynchronous Communication and Message Queuing
date: 29-05-2024
subject: null
keywords: null
subtitle: null
titlepage: true
titlepage-text-color: '663366'
titlepage-rule-color: '663366'
titlepage-background: null
titlepage-rule-height: null
page-background: Bins/Pandoc-extras/backgrounds/background6.pdf
titlepage-logo: Bins/Pandoc-extras/fontys-logo.pdf
logo-width: 50mm
toc: true
toc-own-page: true
header-left: \hspace{1cm}
header-center: \leftmark
header-right: Page \thepage
footer-left: \thetitle
footer-center: null
footer-right: \theauthor
tags: null
updated: 2024-05-29T15:16
---

# RabbitMQ Integration for Asynchronous Communication and Message Queuing

# 1. Summary

This document outlines the potential benefits and implementation considerations of integrating RabbitMQ, a robust and scalable message broker, into the MATLAB Production Server API wrapper project. RabbitMQ would serve as a message queue to facilitate asynchronous communication between the API wrapper and the MATLAB server, enhancing system performance, reliability, and scalability.

# 2. Introduction

## 2.1 Background

In the current system architecture, the C# API wrapper communicates directly with the MATLAB server to submit calculations and retrieve results. While this approach works well for moderate workloads, it can become a bottleneck as the number of requests increases.

Introducing a message queue like RabbitMQ can address this limitation by decoupling the API wrapper and the MATLAB server. The API wrapper would send messages (representing calculation requests) to the queue, and the MATLAB server would consume messages from the queue, process them, and send results back to the API wrapper (potentially through another queue).

## 2.2 Objectives

* Implement asynchronous communication between the API wrapper and MATLAB server using RabbitMQ.
* Improve system scalability and responsiveness under high load.
* Enhance fault tolerance by buffering messages in the queue in case the MATLAB server is temporarily unavailable.
* Provide a foundation for future expansion of the system with additional message-based communication patterns.

# 3. Methodology

## 3.1 Architecture

### 3.1.1 General Overview

````mermaid
flowchart LR

App([External Application])

subgraph "Matlab Calculation Module"
  Controller(C# Controller)
  RabbitMQ(RabbitMQ)
  Matlab(MATLAB Production Server)
  subgraph "Data Storage (Docker)"
    Database[(Postgres Database)]
    MinIO[(MinIO Object Storage)]
  end
end

App -->|"HTTP Request (Query)"| Controller
Controller -->|"Publish Message (Query)"| RabbitMQ
RabbitMQ -->|"Deliver Message (Query)"| Matlab
Matlab -->|"Publish Message (Results)"| RabbitMQ
RabbitMQ -->|"Deliver Message (Results)"| Controller
Controller -->|"HTTP Response (Data, Image)"| App
Controller <-->|"Store/Retrieve (Request, Response, Logs)"| Database
Controller -->|"Store/Retrieve Generated Image"| MinIO
````

![](Bins/Images/APIgeneralViewRabbit.png)

### 3.1.2 Detailed Overview

````mermaid
flowchart LR

subgraph "Matlab Calculation Module"
  Controller(C# Controller)
  subgraph RabbitMQ
      ReqQ[calculation_requests queue]
      ResQ[calculation_results queue]
   end
  Matlab(MATLAB Production Server)
end
  
Controller -->|"Publish Message (Query)"| ReqQ
ReqQ -->|"Deliver Message (Query)"| Matlab
Matlab -->|"Publish Message (Results)"| ResQ
ResQ -->|"Deliver Message (Results)"| Controller
````

![](Bins/Images/rabbit.png)

## 3.2 Technologies

* **RabbitMQ:** Open-source message broker.
* **AMQP 0-9-1:** Messaging protocol used by RabbitMQ.
* **C#:** Programming language used for the API wrapper's interaction with RabbitMQ.
* **RabbitMQ .NET Client Library:** Library for connecting to and interacting with RabbitMQ from .NET applications.
* **Docker:** Containerization platform for deploying RabbitMQ.

## 3.3 Configuration

### 3.3.1 Deployment and Orchestration

RabbitMQ is deployed as a container within the same Docker Compose environment as the other components of your system. The `docker-compose.yml` file handles the orchestration, ensuring that RabbitMQ starts up alongside the MATLAB server, API wrapper, database, and MinIO.

The relevant section in `docker-compose.yml` for RabbitMQ is:

````yaml
rabbitmq:
image: rabbitmq:3.13-management
hostname: rabbitmq
ports:
    - "5672:5672"  # AMQP protocol port
    - "15672:15672" # Management UI port
environment:
    - RABBITMQ_DEFAULT_USER=$RABBITMQ_USER
    - RABBITMQ_DEFAULT_PASS=$RABBITMQ_PASS
networks:
    - matlab-network
````

### 3.3.2 Environment Variables (`.env`)

The `.env` file stores sensitive configuration details, including the RabbitMQ credentials:

* `RABBITMQ_USER`: Specifies the username for accessing the RabbitMQ server (default user is set to `teamium`).
* `RABBITMQ_PASS`: Sets the password for the RabbitMQ user (default password is set to `teamium666`).

### 3.3.3 Key Configuration Details

* **Image:** The `rabbitmq:3.13-management` image is used, which includes the RabbitMQ server and a management plugin for easy monitoring and administration.
* **Hostname:** The hostname is set to `rabbitmq`, making it easier to reference within the Docker network.
* **Ports:**
  * 5672: The standard AMQP port for client connections.
  * 15672: The port for accessing the RabbitMQ management interface.
* **Environment Variables:** The `RABBITMQ_DEFAULT_USER` and `RABBITMQ_DEFAULT_PASS` variables are used to set the default credentials for the RabbitMQ server.
* **Networking:** The `matlab-network` bridge network allows the RabbitMQ container to communicate with other containers in the environment.

### 3.3.4 Management Interface

The RabbitMQ management interface, accessible at `http://localhost:15672`, provides a web-based UI for:

* Monitoring queues, exchanges, and connections.
* Managing users and permissions.
* Viewing message rates and other statistics.

### 3.3.5 Security Considerations

* **Credentials:** The RabbitMQ credentials (`RABBITMQ_USER` and `RABBITMQ_PASS`) should be kept confidential, as they grant administrative access to the message broker.
* **Network Security:** Consider restricting access to the RabbitMQ management interface and AMQP port to trusted networks or IP addresses.
* **TLS:** Enable Transport Layer Security (TLS) to encrypt communication between the API wrapper, MATLAB server, and RabbitMQ.

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #kubernetes
````
