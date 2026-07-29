---
layout: post
title: "Building an Email Notification Microservice with Spring Boot - Part 2: Project Setup and Structure"
date: 2026-08-01
categories: spring-boot microservices java
---

# Building an Email Notification Microservice with Spring Boot

## Part 2: Creating the Spring Boot Project Structure, Dependencies, and Initial Code

In the previous article, we discussed the purpose of an Email Notification Microservice and designed the high-level architecture.

In this article, we will start building the application.

We will:

- Create the Spring Boot project
- Add required dependencies
- Design the package structure
- Configure application properties
- Create the first REST API skeleton
- Understand the responsibility of each layer

By the end of this article, we will have a running Spring Boot microservice ready for email functionality.

---

# Prerequisites

Before starting, make sure you have:

- Java 17 or above
- Maven 3.8+
- IDE (IntelliJ IDEA, Eclipse, or VS Code)
- Postman (for API testing)

You can verify your Java installation:

```bash
java -version
```

Example output:

```
java version "17.0.x"
```

---

# Creating the Spring Boot Project

We will use Spring Initializr to generate our project.

Open:

```
https://start.spring.io
```

Select the following options:

## Project

```
Maven
```

## Language

```
Java
```

## Spring Boot Version

```
3.x.x
```

## Project Details

```
Group:
com.example

Artifact:
email-notification-service

Name:
email-notification-service

Package name:
com.example.emailnotification
```

## Dependencies

Add the following dependencies:

### Spring Web

Used for creating REST APIs.

```
Spring Web
```

---

### Spring Mail

Used for sending emails.

```
Java Mail Sender
```

---

### Validation

Used for request validation.

```
Validation
```

---

### Lombok

Reduces boilerplate code.

```
Lombok
```

---

### Spring Boot DevTools

Improves development experience.

```
Spring Boot DevTools
```

---

Download the project and open it in your IDE.

---

# Project Structure

A clean package structure helps maintain the application as it grows.

Our initial structure:

```
email-notification-service
|
├── src/main/java
│
└── com.example.emailnotification
        |
        ├── controller
        |
        ├── service
        |
        ├── dto
        |
        ├── model
        |
        ├── config
        |
        └── EmailNotificationApplication.java
```

---

# Understanding Each Package

## Controller Layer

Location:

```
controller
```

Responsibility:

- Handle HTTP requests
- Validate input
- Return API responses

Example:

```
POST /api/emails/send
```

---

## Service Layer

Location:

```
service
```

Responsibility:

Contains business logic.

Examples:

- Prepare email
- Validate recipients
- Call email provider

---

## DTO Layer

Location:

```
dto
```

DTO stands for Data Transfer Object.

It represents data received from external applications.

Example:

```java
EmailRequest
```

---

## Model Layer

Location:

```
model
```

Contains domain objects.

Examples:

```
Email
EmailStatus
```

---

## Config Layer

Location:

```
config
```

Contains application configuration.

Examples:

- Email configuration
- Security configuration
- RabbitMQ configuration
- Kafka configuration

---

# Maven Dependencies

After generating the project, your `pom.xml` will contain dependencies similar to:

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>
            spring-boot-starter-web
        </artifactId>
    </dependency>


    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>
            spring-boot-starter-mail
        </artifactId>
    </dependency>


    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>
            spring-boot-starter-validation
        </artifactId>
    </dependency>


    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>
            lombok
        </artifactId>
    </dependency>

</dependencies>
```

---

# Application Configuration

Open:

```
application.properties
```

Add:

```properties
spring.application.name=email-notification-service

server.port=8081
```

Our service will run on:

```
http://localhost:8081
```

---

# Creating the Main Application Class

Spring Boot creates the main class automatically.

File:

```
EmailNotificationApplication.java
```

Code:

```java
package com.example.emailnotification;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


@SpringBootApplication
public class EmailNotificationApplication {

    public static void main(String[] args) {

        SpringApplication.run(
            EmailNotificationApplication.class,
            args
        );
    }
}
```

The annotation:

```java
@SpringBootApplication
```

enables:

- Component scanning
- Auto configuration
- Spring Boot configuration

---

# Creating the Email Request DTO

Create:

```
dto/EmailRequest.java
```

Code:

```java
package com.example.emailnotification.dto;


import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;

import lombok.Data;


@Data
public class EmailRequest {


    @NotBlank
    @Email
    private String to;


    @NotBlank
    private String subject;


    @NotBlank
    private String message;

}
```

This class represents an email request.

Example JSON:

```json
{
    "to": "customer@example.com",
    "subject": "Welcome",
    "message": "Welcome to our application"
}
```

---

# Creating the Email Controller

Create:

```
controller/EmailController.java
```

Code:

```java
package com.example.emailnotification.controller;


import com.example.emailnotification.dto.EmailRequest;

import jakarta.validation.Valid;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;


@RestController
@RequestMapping("/api/emails")
public class EmailController {


    @PostMapping("/send")
    public ResponseEntity<String> sendEmail(
            @Valid @RequestBody EmailRequest request
    ) {


        return ResponseEntity.ok(
            "Email request received"
        );
    }

}
```

---

# Running the Application

Start the application.

You should see:

```
Started EmailNotificationApplication
```

The service is now running.

---

# Testing the API

Using Postman:

URL:

```
POST http://localhost:8081/api/emails/send
```

Headers:

```
Content-Type: application/json
```

Request body:

```json
{
    "to":"user@example.com",
    "subject":"Test Email",
    "message":"Hello from notification service"
}
```

Response:

```
Email request received
```

---

# Current Architecture

At this stage:

```
Client
  |
  |
  v

REST Controller

  |
  |
  v

Email Request DTO

  |
  |
  v

(Service will be added next)
```

---

# What We Have Completed

In this article, we created:

✅ Spring Boot project  
✅ Maven configuration  
✅ Package structure  
✅ REST controller  
✅ Request DTO  
✅ Application configuration  

---

# What's Next?

In Part 3, we will implement the actual email sending capability.

We will:

- Configure SMTP
- Create Email Service
- Use Spring Mail Sender
- Send our first real email
- Handle email exceptions

Next Article:

➡️ **Part 3 - Sending Emails Using Spring Boot and SMTP**
