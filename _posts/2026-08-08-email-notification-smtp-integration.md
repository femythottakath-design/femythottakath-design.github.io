---
layout: post
title: "Building an Email Notification Microservice with Spring Boot - Part 3: Sending Emails Using SMTP and Spring Mail"
date: 2026-08-08
categories: spring-boot smtp email microservices
---

# Building an Email Notification Microservice with Spring Boot

## Part 3: Sending Emails Using SMTP and Spring Mail

In the previous article, we created the foundation of our Email Notification Microservice.

We created:

- Spring Boot project
- REST API endpoint
- Request DTO
- Application structure

Currently, our API only receives email requests.

The next step is to actually send emails.

In this article, we will implement:

- SMTP configuration
- Spring Mail integration
- Email service layer
- Email sending logic
- Exception handling

---

# Understanding SMTP

SMTP stands for:

**Simple Mail Transfer Protocol**

It is the standard protocol used to send emails across the internet.

The email flow looks like this:

```
Application
      |
      |
      v
SMTP Server
      |
      |
      v
Recipient Mail Server
      |
      |
      v
User Inbox
```

Examples of SMTP providers:

- Gmail SMTP
- Outlook SMTP
- Amazon SES
- SendGrid
- Mailgun

---

# Adding Spring Mail Dependency

First, add Spring Mail dependency.

Open:

```
pom.xml
```

Add:

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-mail
    </artifactId>
</dependency>
```

Spring Boot provides:

```
JavaMailSender
```

which simplifies email communication.

---

# SMTP Configuration

Open:

```
application.properties
```

Add:

```properties
spring.mail.host=smtp.gmail.com
spring.mail.port=587

spring.mail.username=your-email@gmail.com
spring.mail.password=your-password

spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

Explanation:

| Property | Purpose |
|---|---|
| host | SMTP server address |
| port | SMTP communication port |
| username | Email account |
| password | Email authentication |
| starttls | Secure connection |

---

# Security Note

Do not store passwords directly inside:

```
application.properties
```

For production systems, use:

- Environment variables
- AWS Secrets Manager
- Azure Key Vault
- HashiCorp Vault

Example:

```properties
spring.mail.username=${EMAIL_USERNAME}

spring.mail.password=${EMAIL_PASSWORD}
```

---

# Creating Email Service Layer

The controller should not contain email logic.

The responsibility should be separated:

```
Controller
     |
     |
     v
Service
     |
     |
     v
Email Provider
```

Create:

```
service/EmailService.java
```

Code:

```java
package com.example.emailnotification.service;


import com.example.emailnotification.dto.EmailRequest;


public interface EmailService {

    void sendEmail(EmailRequest request);

}
```

---

# Creating Email Service Implementation

Create:

```
service/EmailServiceImpl.java
```

Code:

```java
package com.example.emailnotification.service;


import com.example.emailnotification.dto.EmailRequest;

import lombok.RequiredArgsConstructor;

import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;

import org.springframework.stereotype.Service;


@Service
@RequiredArgsConstructor
public class EmailServiceImpl 
        implements EmailService {


    private final JavaMailSender mailSender;


    @Override
    public void sendEmail(
            EmailRequest request
    ) {

        SimpleMailMessage message =
                new SimpleMailMessage();


        message.setTo(request.getTo());

        message.setSubject(
                request.getSubject()
        );

        message.setText(
                request.getMessage()
        );


        mailSender.send(message);
    }
}
```

---

# Updating the Controller

Now connect the controller with the service.

Update:

```
EmailController.java
```

Code:

```java
@RestController
@RequestMapping("/api/emails")
@RequiredArgsConstructor
public class EmailController {


    private final EmailService emailService;


    @PostMapping("/send")
    public ResponseEntity<String> sendEmail(
            @Valid @RequestBody EmailRequest request
    ) {


        emailService.sendEmail(request);


        return ResponseEntity.ok(
            "Email sent successfully"
        );
    }

}
```

---

# Testing Email Sending

Start the application.

Send request:

```
POST

http://localhost:8081/api/emails/send
```

Request:

```json
{
 "to":"customer@example.com",
 "subject":"Welcome",
 "message":"Welcome to our application"
}
```

Expected response:

```
Email sent successfully
```

The recipient should receive:

```
Subject:
Welcome

Message:
Welcome to our application
```

---

# Current Architecture

After this implementation:

```
Client Application

        |
        |
        v

Email Controller

        |
        |
        v

Email Service

        |
        |
        v

JavaMailSender

        |
        |
        v

SMTP Server

        |
        |
        v

Recipient
```

---

# Improving the Service

The current implementation works, but production systems need additional features:

## Email Templates

Instead of plain text:

```
Hello John,
Welcome!
```

we can create HTML templates.

Example:

```
templates/
   welcome-email.html
   reset-password.html
```

---

## Email Status Tracking

We need to know:

```
PENDING
SENT
FAILED
```

This requires database integration.

---

## Retry Handling

What happens if:

- SMTP server is unavailable?
- Network fails?
- Provider rejects the email?

We need retry mechanisms.

---

## Asynchronous Processing

Currently:

```
User Request
      |
      |
      v
Send Email
      |
      |
      v
Response
```

The user waits until email sending completes.

A better approach:

```
User Request
      |
      |
      v
Queue Message
      |
      |
      v
Send Email Later
```

This is where RabbitMQ will be introduced.

---

# What We Completed

In this article:

✅ SMTP configuration  
✅ Spring Mail integration  
✅ Email service layer  
✅ Email sending functionality  
✅ Controller-service communication  

---

# What's Next?

In Part 4, we will improve our architecture by adding database support.

We will implement:

- Email entity
- PostgreSQL database
- Email history table
- Delivery status tracking
- JPA repository

Next Article:

➡️ **Part 4 - Adding PostgreSQL and Email Status Tracking**
