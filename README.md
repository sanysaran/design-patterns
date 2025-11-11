# design-patterns

# 🧠 Java Microservices Design Patterns — Comprehensive Guide

A practical guide mapping **Creational**, **Structural**, and **Behavioral** design patterns to **real-world examples in Java Microservices / Spring Boot**.

This document also includes **Microservice-Specific Architecture Patterns** commonly used in distributed systems.

---

## 🧱 1. Creational Design Patterns

| **Pattern** | **Purpose** | **Example in Java Microservices / Spring** |
|--------------|-------------|--------------------------------------------|
| **Singleton** | Ensures only one instance of a class exists | Spring beans by default are singletons (e.g., `@Service`, `@Repository` beans). |
| **Factory Method** | Creates objects without specifying exact class | `RestTemplateBuilder`, `WebClient.builder()` in Spring Boot create configured clients. |
| **Abstract Factory** | Creates families of related objects | Spring’s `ApplicationContext` creates multiple related beans from configurations. |
| **Builder** | Simplifies complex object creation step-by-step | `ResponseEntity.ok().body(...)` or building `RestTemplate` / `WebClient`. |
| **Prototype** | Creates object copies | Spring’s `@Scope("prototype")` beans create new instances per request. |
| **Object Pool** | Reuse objects that are expensive to create | Connection pools in HikariCP / Tomcat JDBC pool used by Spring Data JPA. |

---

## 🏗️ 2. Structural Design Patterns

| **Pattern** | **Purpose** | **Example in Java Microservices / Spring** |
|--------------|-------------|--------------------------------------------|
| **Adapter** | Converts one interface to another | Spring `HandlerAdapter` adapts different controller types to `DispatcherServlet`. |
| **Bridge** | Decouples abstraction from implementation | Spring’s `JdbcTemplate` decouples DB operations from specific JDBC drivers. |
| **Composite** | Treats individual and composite objects uniformly | Spring’s `ApplicationContext` hierarchy (parent-child contexts). |
| **Decorator** | Adds new behavior dynamically | Spring `HandlerInterceptor`, `WebFilter`, or AOP for cross-cutting concerns. |
| **Facade** | Provides simplified interface to complex subsystems | REST Controllers exposing simplified APIs that internally use multiple services. |
| **Proxy** | Controls access to another object | Spring Data JPA repositories use proxies for lazy loading and transactions. |
| **Flyweight** | Shares common data to save memory | Caching mechanisms like Spring Cache or Hibernate’s entity caching. |

---

## ⚙️ 3. Behavioral Design Patterns

| **Pattern** | **Purpose** | **Example in Java Microservices / Spring** |
|--------------|-------------|--------------------------------------------|
| **Chain of Responsibility** | Passes request through chain of handlers | Spring Security Filter Chain, Servlet Filter Chain. |
| **Command** | Encapsulates a request as an object | Using `@Async` or message-driven commands with RabbitMQ / Kafka listeners. |
| **Interpreter** | Defines grammar and interprets expressions | Spring Expression Language (SpEL) for annotations like `@PreAuthorize`. |
| **Iterator** | Sequentially access collection elements | Iterating over collections in Spring Data (e.g., `Streamable` interfaces). |
| **Mediator** | Coordinates communication between objects | Spring Application Events (`ApplicationEventPublisher` & listeners). |
| **Memento** | Captures object state for restore | Saving configuration or rollback using transaction management. |
| **Observer** | Notifies dependents of state changes | Spring Events, ApplicationEventPublisher, or Kafka listeners. |
| **State** | Alters behavior based on internal state | State machines using Spring State Machine framework. |
| **Strategy** | Selects algorithm dynamically | Using multiple implementations of an interface with `@Qualifier`. |
| **Template Method** | Defines skeleton of algorithm, lets subclasses override steps | Spring’s `JdbcTemplate`, `RestTemplate`, `TransactionTemplate`. |
| **Visitor** | Separates algorithm from object structure | Processing domain events or DTO transformations using separate visitors. |

---

## ☁️ 4. Microservice-Specific Architecture Patterns

| **Pattern** | **Purpose** | **Example in Spring Microservices** |
|--------------|-------------|------------------------------------|
| **Circuit Breaker** | Prevents cascading failures | Resilience4j / Spring Cloud Circuit Breaker. |
| **API Gateway** | Single entry point for services | Spring Cloud Gateway / Netflix Zuul. |
| **Service Registry & Discovery** | Finds service instances dynamically | Netflix Eureka / Consul / Spring Cloud DiscoveryClient. |
| **Config Server Pattern** | Centralized externalized configuration | Spring Cloud Config Server. |
| **Saga Pattern** | Distributed transaction management | Spring Cloud Data Flow / orchestration tools. |
| **CQRS (Command Query Responsibility Segregation)** | Separate read/write models | Spring Data + Kafka-based event sourcing. |
| **Event Sourcing** | Reconstruct state from event stream | Kafka + Event Store + Spring Boot. |
| **Sidecar Pattern** | Helper microservice for proxying/logging | Sidecar container for metrics or security (e.g., Envoy). |
| **Strangler Fig Pattern** | Incrementally replace legacy apps | Using Spring Cloud Gateway routes to new microservices gradually. |
| **Bulkhead Pattern** | Isolate resources to prevent system-wide failure | Thread pool isolation in Resilience4j / Hystrix. |
| **Aggregator Pattern** | Combine responses from multiple services | REST Controller that calls several microservices via `WebClient`. |

---

## ✅ Summary Table

| **Design Pattern** | **Real Example** |
|---------------------|------------------|
| Proxy | Spring Data JPA Repository |
| Facade | REST Controller |
| Builder | `ResponseEntity` Builder |
| Singleton | `@Service` Bean |
| Chain of Responsibility | Spring Security Filter Chain |
| Template Method | `JdbcTemplate` |
| Strategy | Payment processor via `@Qualifier` |
| Observer | ApplicationEventPublisher |
| Adapter | HandlerAdapter |
| Circuit Breaker | Resilience4j in Spring Cloud |

---

## 🧩 Notes

- Many GoF patterns naturally occur in **Spring’s core framework** — especially through **AOP, Dependency Injection, and Template abstractions**.  
- **Microservice patterns** often build upon these base design patterns, extending them into distributed systems.  
- Understanding these mappings helps design **clean, maintainable, and resilient microservices**.

---

### ✍️ Author
Curated for developers mastering **Spring Boot + Microservice Architecture + Design Patterns**.

You can fork or star ⭐ this repo if it helped you!

---
