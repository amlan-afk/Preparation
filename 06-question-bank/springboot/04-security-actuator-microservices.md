# 🍃 Spring Boot: Security, Microservices & Testing (Q76 – Q100)

---

### Q76: Describe the architecture of Spring Security 6 (Spring Boot 3).
**Direct Answer:**  
Spring Security operates as a chain of servlet filters (`FilterChainProxy` / `SecurityFilterChain`):
1. **SecurityFilterChain:** Bean configuring HTTP security matching rules, CSRF, CORS, session policy, and custom filter orders.
2. **AuthenticationFilter:** Extracts credentials (e.g. username/password, Bearer token) and creates an unauthenticated `Authentication` token.
3. **AuthenticationManager:** Authenticates the token (delegates to one or more `AuthenticationProvider`s).
4. **AuthenticationProvider:** Validates credentials against a data store using `UserDetailsService` and `PasswordEncoder`.
5. **SecurityContextHolder:** Stores the authenticated `Authentication` object in thread-local memory.

---

### Q77: How do you implement stateless JWT authentication in Spring Security?
**Direct Answer:**  
1. Create a `JwtAuthenticationFilter extends OncePerRequestFilter`:
   - Extracts `Authorization: Bearer <token>` header.
   - Validates signature and expiration via `JwtService`.
   - Loads user details and populates `SecurityContextHolder.getContext().setAuthentication(authToken)`.
2. Configure `SecurityFilterChain`:
```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    return http
        .csrf(AbstractHttpConfigurer::disable)
        .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/v1/auth/**").permitAll()
            .anyRequest().authenticated()
        )
        .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
        .build();
}
```

---

### Q78: Why can CSRF protection be safely disabled for stateless REST APIs?
**Direct Answer:**  
**CSRF (Cross-Site Request Forgery)** relies on the browser automatically attaching stored **session cookies** to cross-site requests.  
In a stateless REST API using JWT tokens stored in headers (`Authorization: Bearer ...`), the browser **does not automatically attach the token** to cross-site requests. An attacker website cannot force the user's browser to send the header, making CSRF protection redundant.

---

### Q79: How does Method-Level Authorization work with `@PreAuthorize`?
**Direct Answer:**  
Enabled via `@EnableMethodSecurity`:
```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("#userId == authentication.principal.id or hasRole('MANAGER')")
public UserProfile getProfile(Long userId) { ... }
```
Evaluates Spring Expression Language (SpEL) before invoking the target method. Supports fine-grained RBAC and ABAC checks on method arguments.

---

### Q80: How does `BCryptPasswordEncoder` work under the hood?
**Direct Answer:**  
BCrypt is a slow, salted hashing function based on the Blowfish cipher:
- Automatically generates a **cryptographically secure random 128-bit salt** and embeds it directly into the resulting 60-character hash string.
- Incorporates a **configurable work factor / cost parameter** (default 10 = $2^{10} = 1,024\text{ iterations}$).
- `matches(rawPassword, encodedPassword)` extracts the salt from the stored hash, hashes the raw password with that salt, and compares them using constant-time comparison to prevent timing attacks.

---

### Q81: What is the difference between Spring Cloud Gateway and Netflix Zuul?
**Direct Answer:**  
- **Netflix Zuul 1.x:** Blocking, synchronous architecture. One thread per connection. Struggles under 10,000+ concurrent persistent connections.
- **Spring Cloud Gateway:** Built on **Spring WebFlux and Project Reactor (Netty)**. Fully non-blocking, asynchronous, reactive architecture. Handles high concurrency with significantly lower memory and fewer threads. Supports route predicates, rate limiting, and circuit breaker filters.

---

### Q82: How does Service Discovery work with Eureka or Consul?
**Direct Answer:**  
1. **Service Registration:** Each microservice instance registers its hostname, IP, and port with the Eureka server at startup.
2. **Heartbeats:** Microservices send heartbeat pings every 30 seconds (`eureka.instance.lease-renewal-interval-in-seconds=30`).
3. **Client-Side Discovery:** Client services (or Spring Cloud LoadBalancer) fetch the service registry cache and balance traffic across healthy instances without a centralized hardware load balancer.

---

### Q83: Compare OpenFeign vs `WebClient` vs Spring 6 `RestClient`.
**Direct Answer:**  
- **OpenFeign:** Declarative, annotation-based HTTP client (`@FeignClient`). Synchronous/blocking; great for concise microservice-to-microservice REST calls.
- **`WebClient`:** Part of Spring WebFlux. Fully asynchronous, non-blocking, reactive client. Best for high-concurrency streaming.
- **`RestClient` (Spring Boot 3.2+):** Modern synchronous HTTP client offering a fluent functional API (like `WebClient`), replacing legacy `RestTemplate`.

---

### Q84: How does a Resilience4j Circuit Breaker work?
**Direct Answer:**  
Protects systems from cascading failures by monitoring calls to external dependencies:
- **CLOSED:** Normal state. Calls pass through. If error rate exceeds threshold (e.g. 50%), circuit transitions to **OPEN**.
- **OPEN:** Calls fail immediately without hitting the downstream dependency. Executes fallback method. After a configured wait duration (e.g. 10s), transitions to **HALF_OPEN**.
- **HALF_OPEN:** Allows a limited number of test calls (e.g. 10). If they succeed, resets to **CLOSED**; if they fail, returns to **OPEN**.

---

### Q85: What is the Saga Pattern in Microservices? (Choreography vs Orchestration)
**Direct Answer:**  
Manages distributed transactions across microservices using a sequence of local transactions with **Compensating Transactions** for rollbacks:
- **Choreography (Event-Driven):** Services publish events to Kafka; other services listen and execute local transactions. Decoupled, but hard to trace and debug.
- **Orchestration (Command-Driven):** A dedicated Saga Orchestrator (e.g. Temporal or Spring service) sends explicit commands to each participant and coordinates compensating rollback steps if any service fails.

---

### Q86: What is the Transactional Outbox Pattern and why is it critical?
**Direct Answer:**  
**The Dual-Write Problem:** If a service writes to a DB and then publishes an event to Kafka, the app could crash *between* the DB commit and Kafka send, losing the event!  
**The Outbox Solution:**
1. In the same local database transaction, write business data to the `orders` table AND write the message payload to an `outbox` table.
2. A separate background process (Change Data Capture / Debezium or polling worker) reads from the `outbox` table and publishes to Kafka with guaranteed at-least-once delivery.

---

### Q87: How do you guarantee Idempotent processing in Kafka Consumers?
**Direct Answer:**  
Because Kafka provides **at-least-once** delivery by default, network retries can send duplicate messages:
1. Producer attaches a unique `message_id` or `idempotency_key` (e.g. `order_id`).
2. Consumer uses an **Idempotent Repository (PostgreSQL / Redis)**:
   ```sql
   INSERT INTO processed_messages (message_id) VALUES ('msg-123');
   ```
3. If duplicate message arrives, the DB unique constraint fails, and the consumer acknowledges the offset without re-executing business logic.

---

### Q88: Compare `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest`.
**Direct Answer:**  
- `@SpringBootTest`: Loads the **entire** `ApplicationContext` with all beans. Heavy, slow, used for full end-to-end integration tests.
- `@WebMvcTest(controllers = UserController.class)`: Slices only the **web layer** (controllers, filters, security, converters). Does not load JPA repositories or services. Fast.
- `@DataJpaTest`: Slices only the **JPA repository layer**. Configures an in-memory or Testcontainers DB, enables SQL logging, and makes every test transactional by default (auto-rollback).

---

### Q89: How do Testcontainers revolutionize Spring Boot integration testing?
**Direct Answer:**  
Historically, tests used H2 in-memory databases, which missed dialect-specific PostgreSQL features (JSONB, spatial indexes, stored procs).  
**Testcontainers** uses the Docker API to spin up real, disposable container instances of PostgreSQL, Redis, Kafka, or RabbitMQ for integration test suites:
```java
@Container
static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");
```
Guarantees 100% parity between test and production environments.

---

### Q90: What is the difference between `@MockBean` and `@SpyBean`?
**Direct Answer:**  
- `@MockBean`: Creates a Mockito mock that **completely replaces** the real bean in the `ApplicationContext`. All methods return default values (`null`, `0`) unless explicitly stubbed (`when(...).thenReturn(...)`).
- `@SpyBean`: Wraps the **real existing bean** in a Mockito spy. Real methods execute by default unless specifically stubbed.

---

### Q91: How do Spring Boot Layered JARs optimize Docker image build caching?
**Direct Answer:**  
In a standard fat JAR, application code and dependencies are bundled into one archive. A 1-line code change requires re-uploading all 100MB of dependencies!  
**Layered JARs (`jarmode=layertools`):** Splits the JAR into 4 distinct layers:
1. `dependencies` (rarely changes - cached Docker layer)
2. `spring-boot-loader`
3. `snapshot-dependencies`
4. `application` (your code - small, rebuilt in $< 1\text{s}$)

```dockerfile
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=builder /extracted/dependencies/ ./
COPY --from=builder /extracted/spring-boot-loader/ ./
COPY --from=builder /extracted/snapshot-dependencies/ ./
COPY --from=builder /extracted/application/ ./
ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]
```

---

### Q92: What are the key breaking changes when migrating from Spring Boot 2.x to 3.x?
**Direct Answer:**  
1. **Java 17 Baseline:** Java 8 and 11 are no longer supported.
2. **Jakarta EE 9/10 Namespace:** All `javax.*` imports changed to `jakarta.*` (e.g. `jakarta.persistence.*`, `jakarta.servlet.*`, `jakarta.validation.*`).
3. **Spring Security 6:** `SecurityFilterChain` bean required; `WebSecurityConfigurerAdapter` completely removed.
4. **Native Compilation:** First-class support for GraalVM native images.
5. **Observability:** Built-in Micrometer Tracing replaces Spring Cloud Sleuth.

---

### Q93: How does the Bulkhead pattern protect microservices in Resilience4j?
**Direct Answer:**  
Inspired by ship bulkheads that compartmentalize hulls to prevent sinking from a single leak:
- **Semaphore Bulkhead:** Limits concurrent active calls to a specific dependency (e.g. max 10 concurrent requests to Payment Gateway).
- **ThreadPool Bulkhead:** Allocates a dedicated thread pool for a specific downstream client.
- Prevents one slow downstream service from exhausting all Tomcat threads and taking down the entire application.

---

### Q94: What is Contract Testing with Spring Cloud Contract or Pact?
**Direct Answer:**  
Contract testing validates that a producer and consumer microservice agree on API request/response schemas **without needing to spin up both services simultaneously**:
- Consumer defines expectations in a contract file (Pact / Groovy).
- Producer generates automated tests from the contract to ensure its API never breaks the expected format.
- Consumer uses generated stub runners in local tests instead of calling live services.

---

### Q95: How do you handle database deadlocks and concurrency in high-volume microservices?
**Direct Answer:**  
1. Retry with jitter: Use `@Retryable(retryFor = CannotAcquireLockException.class, maxAttempts = 3, backoff = @Backoff(delay = 100, maxDelay = 1000, random = true))`.
2. Consistent lock acquisition ordering across transactions.
3. Keep transactions as short as possible (do not make external HTTP/API calls inside `@Transactional`).

---

### Q96: How do you configure SSL/TLS Termination at the API Gateway vs Ingress Controller?
**Direct Answer:**  
- **Edge Termination:** SSL is terminated at the Cloud Load Balancer (AWS ALB) or Kubernetes Ingress (NGINX). Traffic inside the internal cluster network runs over HTTP to save CPU overhead.
- **End-to-End TLS (mTLS):** Required for strict security (PCI-DSS, healthcare HIPAA). Gateway terminates public TLS and uses Mutual TLS (mTLS) with internal service mesh (Istio/Linkerd) to verify client certificates across microservices.

---

### Q97: What is Spring Cloud Config Server and how does it secure secrets?
**Direct Answer:**  
Provides centralized, version-controlled configuration across environments (Dev, QA, Prod):
- Backed by Git, Vault, or AWS Secrets Manager.
- Encrypts sensitive secrets at rest using symmetric (AES) or asymmetric (RSA) keys (`{cipher}AQB...`).
- Decrypts secrets on-the-fly before serving to authorized microservices over HTTPS.

---

### Q98: How do you implement Distributed Caching with Redis in Spring Boot?
**Direct Answer:**  
1. Add `spring-boot-starter-data-redis` and `spring-boot-starter-cache`.
2. Configure `RedisCacheManager` with default TTL, null value disabling, and Jackson JSON serializer:
```java
@Bean
public RedisCacheConfiguration cacheConfig() {
    return RedisCacheConfiguration.defaultCacheConfig()
        .entryTtl(Duration.ofMinutes(10))
        .disableCachingNullValues()
        .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
}
```

---

### Q99: How do you secure Actuator in production?
**Direct Answer:**  
1. Separate management port: `management.server.port=8081` (binds to private VPC subnet, unexposed to public internet).
2. Protect with Spring Security: Require `ADMIN` role for `/actuator/**`.
3. Disable all endpoints by default, explicitly whitelisting only safe endpoints:
   ```properties
   management.endpoints.web.exposure.include=health,metrics,prometheus
   management.endpoint.health.show-details=when-authorized
   ```

---

### Q100: How do you implement Zero-Downtime Deployments with Spring Boot on Kubernetes?
**Direct Answer:**  
1. Configure **Readiness Probe** (`/actuator/health/readiness`): K8s routes traffic only after the app has warmed up caches and DB pools.
2. Configure **Liveness Probe** (`/actuator/health/liveness`): K8s restarts container only if deadlocked.
3. Enable **Graceful Shutdown** (`server.shutdown=graceful`) with a 30s termination grace period (`preStop` sleep 5s to allow K8s iptables to propagate endpoints).
4. Use Rolling Updates (`maxSurge=25%`, `maxUnavailable=0`).
