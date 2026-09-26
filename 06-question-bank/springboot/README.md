# 🍃 Spring Boot Interview Mastery: Top 100 Questions & Answers

A comprehensive collection of **100 high-yield, production-grade Spring Boot interview questions and answers** for SDE 2 and Senior Backend Engineers. Divided into 4 structured modules covering IoC/DI architecture, web and REST services, JPA/Hibernate persistence, and security/microservices.

---

## 🗺️ Curriculum Module Index

| Module | Topic Range | File Link | Question Count | Core Subjects Covered |
|:---:|---|---|:---:|---|
| **01** | **Core IoC, DI, Bean Lifecycle & Config** | [01-ioc-di-and-bean-lifecycle.md](./01-ioc-di-and-bean-lifecycle.md) | **Q1 – Q25** | Auto-configuration internals, bean scopes, complete lifecycle, constructor injection, AOP proxies, circular dependencies. |
| **02** | **REST APIs, Web Layer & Observability** | [02-mvc-rest-and-exception-handling.md](./02-mvc-rest-and-exception-handling.md) | **Q26 – Q50** | Request lifecycle, `@RestControllerAdvice`, Bean Validation, ProblemDetail RFC 7807, CORS, Actuator, Micrometer & Prometheus. |
| **03** | **Data Access, JPA, Hibernate & Caching** | [03-spring-data-jpa-hibernate.md](./03-spring-data-jpa-hibernate.md) | **Q51 – Q75** | N+1 problem (`JOIN FETCH`), `@Transactional` internals, isolation & propagation, optimistic/pessimistic locks, HikariCP, Redis caching. |
| **04** | **Security, Microservices & Testing** | [04-security-actuator-microservices.md](./04-security-actuator-microservices.md) | **Q76 – Q100** | Spring Security 6, JWT, Resilience4j circuit breakers, Outbox pattern, Saga, Testcontainers, layered Docker JARs. |
| | **TOTAL** | | **100 Questions** | **100% Comprehensive SDE 2 Mastery** |

---

## 🎯 Master List of All 100 Questions

### Module 01: Core IoC, DI & Configuration (Q1 – Q25)
1. What is Spring Boot and how does it differ from traditional Spring?
2. What annotations make up `@SpringBootApplication`?
3. How does Spring Boot Auto-Configuration work under the hood?
4. What is Inversion of Control (IoC) and Dependency Injection (DI)?
5. Why is Constructor Injection strongly preferred over Field Injection?
6. What are the Bean Scopes in Spring Boot?
7. What happens when a prototype bean is injected into a singleton bean?
8. Describe the complete lifecycle of a Spring Bean.
9. What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?
10. Why does `@Configuration` use CGLIB proxying?
11. How do `@Primary` and `@Qualifier` resolve ambiguous dependency injection?
12. What is the difference between `@Value` and `@ConfigurationProperties`?
13. What is the order of precedence for Spring Boot configuration sources?
14. How does Spring Boot detect and prevent Circular Dependencies?
15. What is Spring AOP and what are its key concepts?
16. How does Spring implement AOP proxies: JDK Dynamic Proxies vs CGLIB?
17. What is the difference between `BeanFactory` and `ApplicationContext`?
18. What is the difference between `CommandLineRunner` and `ApplicationRunner`?
19. How do Spring Application Events work (`ApplicationEventPublisher`)?
20. What is Lazy Initialization in Spring Boot and what are its trade-offs?
21. What are Conditional Annotations and how do they work?
22. How does Spring Boot run without an external Tomcat server?
23. What is Spring Native and GraalVM Ahead-Of-Time (AOT) compilation?
24. How do you create a Custom Spring Boot Starter?
25. How do you gracefully reload configuration properties without restarting?

### Module 02: REST APIs & Web Layer (Q26 – Q50)
26. Trace the complete HTTP Request Lifecycle in Spring Boot.
27. What is the difference between `@Controller` and `@RestController`?
28. What is the difference between `@PathVariable`, `@RequestParam`, `@RequestBody`, and `@RequestHeader`?
29. How do you implement robust Global Exception Handling in Spring Boot?
30. What is RFC 7807 `ProblemDetail` in Spring Boot 3?
31. How does Bean Validation work with `@Valid` vs `@Validated`?
32. How do you write a Custom Bean Validation Annotation?
33. Why should JPA Entities NEVER be returned directly from REST Controllers?
34. What is Content Negotiation in Spring Boot?
35. How do you configure CORS in Spring Boot?
36. What is the difference between a Servlet `Filter` and a `HandlerInterceptor`?
37. How do you handle Asynchronous HTTP Requests in Spring Boot?
38. How do Server-Sent Events (SSE) work in Spring Boot?
39. What are the main REST API Versioning strategies in Spring Boot?
40. How do you implement Rate Limiting in Spring Boot?
41. How do you stream large file uploads without blowing up heap memory?
42. What is Spring Boot Actuator and what are its most critical endpoints?
43. How do you write a Custom `HealthIndicator` in Spring Boot?
44. How does Graceful Shutdown work in Spring Boot 2.3+?
45. How do Micrometer, Prometheus, and Grafana work together?
46. How do you record Custom Metrics using Micrometer?
47. How does Distributed Tracing work with Micrometer Tracing / OpenTelemetry?
48. How do you tune Embedded Tomcat thread pools for high QPS?
49. What is `OpenEntityManagerInView` (OSIV) and why should it be disabled?
50. How do you enable HTTP/2 in Spring Boot?

### Module 03: Data Access, JPA & Hibernate (Q51 – Q75)
51. How do Spring Data JPA, Hibernate, and JDBC relate to each other?
52. What is the hierarchy of Repository interfaces in Spring Data?
53. What is the N+1 Query Problem in JPA and how do you resolve it?
54. What causes `LazyInitializationException` and how do you resolve it?
55. What is the difference between First-Level and Second-Level Cache in Hibernate?
56. How does the `@Transactional` annotation work under the hood?
57. Why does `@Transactional` NOT roll back on checked exceptions by default?
58. Why does `@Transactional` fail when called from within the same class?
59. What are the Spring Transaction Propagation levels?
60. What are the Transaction Isolation levels and what anomalies do they prevent?
61. What is the difference between Optimistic Locking and Pessimistic Locking?
62. What is the difference between `orphanRemoval = true` and `CascadeType.REMOVE`?
63. Describe the four Entity Lifecycle states in JPA.
64. How does Hibernate Dirty Checking work?
65. How do you configure and size the HikariCP connection pool in Spring Boot?
66. What is the difference between Flyway and Liquibase?
67. How do you implement Read/Write Database Splitting in Spring Boot?
68. How do Spring Caching annotations work (`@Cacheable`, `@CachePut`, `@CacheEvict`)?
69. Why do bulk updates in JPA require `@Modifying` and `clearAutomatically = true`?
70. How do JPA Specifications implement dynamic multi-criteria search queries?
71. What is the difference between `save()` and `saveAndFlush()` in Spring Data JPA?
72. How does Auditing work in Spring Data JPA?
73. What is the difference between `@Embedded` and `@Embeddable` in JPA?
74. How do you implement Database Multi-Tenancy in Spring Data JPA?
75. How does Hibernate handle primary key generation: `IDENTITY` vs `SEQUENCE`?

### Module 04: Security, Microservices & Testing (Q76 – Q100)
76. Describe the architecture of Spring Security 6 (Spring Boot 3).
77. How do you implement stateless JWT authentication in Spring Security?
78. Why can CSRF protection be safely disabled for stateless REST APIs?
79. How does Method-Level Authorization work with `@PreAuthorize`?
80. How does `BCryptPasswordEncoder` work under the hood?
81. What is the difference between Spring Cloud Gateway and Netflix Zuul?
82. How does Service Discovery work with Eureka or Consul?
83. Compare OpenFeign vs `WebClient` vs Spring 6 `RestClient`.
84. How does a Resilience4j Circuit Breaker work?
85. What is the Saga Pattern in Microservices? (Choreography vs Orchestration)
86. What is the Transactional Outbox Pattern and why is it critical?
87. How do you guarantee Idempotent processing in Kafka Consumers?
88. Compare `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest`.
89. How do Testcontainers revolutionize Spring Boot integration testing?
90. What is the difference between `@MockBean` and `@SpyBean`?
91. How do Spring Boot Layered JARs optimize Docker image build caching?
92. What are the key breaking changes when migrating from Spring Boot 2.x to 3.x?
93. How does the Bulkhead pattern protect microservices in Resilience4j?
94. What is Contract Testing with Spring Cloud Contract or Pact?
95. How do you handle database deadlocks and concurrency in microservices?
96. How do you configure SSL/TLS Termination at the API Gateway vs Ingress?
97. What is Spring Cloud Config Server and how does it secure secrets?
98. How do you implement Distributed Caching with Redis in Spring Boot?
99. How do you secure Actuator in production?
100. How do you implement Zero-Downtime Deployments with Spring Boot on Kubernetes?
