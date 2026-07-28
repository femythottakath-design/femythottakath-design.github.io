---
layout: post
title: "Building an Email Notification Microservice with Spring Boot - Part 4: PostgreSQL and Email Status Tracking"
date: 2026-08-15
categories: spring-boot postgresql jpa microservices
---

# Building an Email Notification Microservice with Spring Boot

## Part 4: Adding PostgreSQL and Email Status Tracking

In the previous article, we successfully sent emails using Spring Boot and SMTP.

However, our application currently has one major limitation:

We do not know:

- Which emails were sent
- Which emails failed
- When emails were sent
- How many retries occurred

A production notification service needs email tracking.

In this article, we will add:

- PostgreSQL database
- Spring Data JPA
- Email entity
- Email status tracking
- Repository layer

---

# Updated Architecture

After adding the database:

```
Client

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
 +----------------+
 |
 v

SMTP Server


 |
 v

PostgreSQL Database
```

---

# Adding Database Dependencies

Update `pom.xml`:

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-data-jpa
    </artifactId>
</dependency>


<dependency>
    <groupId>
        org.postgresql
    </groupId>

    <artifactId>
        postgresql
    </artifactId>
</dependency>
```

---

# Database Configuration

application.properties:

```properties
spring.datasource.url=
jdbc:postgresql://localhost:5432/emaildb

spring.datasource.username=postgres

spring.datasource.password=password


spring.jpa.hibernate.ddl-auto=update

spring.jpa.show-sql=true
```

---

# Creating Email Status Enum

Create:

```
model/EmailStatus.java
```

```java
public enum EmailStatus {

    PENDING,

    SENT,

    FAILED
}
```

---

# Creating Email Entity

Create:

```
model/Email.java
```

```java
@Entity
@Data
public class Email {


@Id
@GeneratedValue
private Long id;


private String recipient;


private String subject;


private String message;


@Enumerated(EnumType.STRING)
private EmailStatus status;


private LocalDateTime createdAt;

}
```

---

# Creating Repository

Create:

```
repository/EmailRepository.java
```

```java
@Repository
public interface EmailRepository
extends JpaRepository<Email,Long>{

}
```

---

# Updating Email Service

Before sending:

```
PENDING
```

After successful delivery:

```
SENT
```

If an error occurs:

```
FAILED
```

Example:

```java
try {

mailSender.send(message);

email.setStatus(
EmailStatus.SENT
);


}
catch(Exception e){

email.setStatus(
EmailStatus.FAILED
);

}
```

---

# Email Lifecycle

```
Email Request

      |
      v

PENDING

      |
      |
      v

SMTP Processing

      |
      |
 +----+----+

 |         |

SUCCESS   ERROR


 |         |

SENT     FAILED
```

---

# Benefits

Now our service can:

- Track email history
- Audit delivery
- Generate reports
- Retry failed emails
- Monitor performance

---

# What's Next?

The current implementation still has a problem.

The user request waits until the email is sent.

For example:

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

For better scalability, we need asynchronous processing.

In Part 5, we will introduce RabbitMQ.

Next Article:

➡️ Part 5 - Integrating RabbitMQ for Reliable Email Processing
