# 🍃 Spring Boot: REST APIs, Web Layer & Observability (Q26 – Q50)

---

### Q26: Trace the complete HTTP Request Lifecycle in Spring Boot.
**Direct Answer:**  
1. **Network & Servlet Filter:** Request enters through embedded Tomcat and passes through the `Filter` chain (security, logging, CORS).
2. **DispatcherServlet:** The front controller receives the request.
3. **HandlerMapping:** Determines the appropriate controller method based on URL path and HTTP verb.
4. **HandlerInterceptor (`preHandle`):** Pre-processing (auth tokens, metric timers).
5. **HandlerAdapter:** Prepares parameters and invokes the target controller method.
6. **HttpMessageConverter:** Deserializes JSON request body into Java DTOs using Jackson.
7. **Controller Execution:** Executes business logic and returns a DTO or `ResponseEntity`.
8. **HttpMessageConverter:** Serializes the return object into JSON response body.
9. **HandlerInterceptor (`postHandle`, `afterCompletion`):** Post-processing and cleanup.
10. **Filter Chain & Response:** Response flows back through filters to the client.

---

### Q27: What is the difference between `@Controller` and `@RestController`?
**Direct Answer:**  
- `@Controller`: Traditional Spring MVC annotation. Methods return a `String` representing a **View name** (e.g. `index.html`), resolved by a `ViewResolver` (Thymeleaf/JSP).
- `@RestController`: Specialized meta-annotation combining `@Controller` and `@ResponseBody`. Tells Spring that method return values should be serialized directly into the HTTP response body (JSON/XML) via `HttpMessageConverter`, completely bypassing view resolution.

---

### Q28: What is the difference between `@PathVariable`, `@RequestParam`, `@RequestBody`, and `@RequestHeader`?
**Direct Answer:**  
- `@PathVariable`: Extracts URI template variables: `/users/{id}` $\rightarrow$ `@PathVariable("id") Long id`.
- `@RequestParam`: Extracts query parameters: `/users?status=active&page=2` $\rightarrow$ `@RequestParam String status`. Also handles `multipart/form-data`.
- `@RequestBody`: Deserializes the HTTP request body JSON/XML into a Java object: `@RequestBody @Valid UserDto dto`.
- `@RequestHeader`: Extracts HTTP headers: `@RequestHeader("Authorization") String token`.

---

### Q29: How do you implement robust Global Exception Handling in Spring Boot?
**Direct Answer:**  
Using `@RestControllerAdvice` and `@ExceptionHandler`:
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex, HttpServletRequest req) {
        ErrorResponse err = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            Instant.now(),
            req.getRequestURI()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(err);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        return ResponseEntity.badRequest().body(errors);
    }
}
```

---

### Q30: What is RFC 7807 `ProblemDetail` in Spring Boot 3?
**Direct Answer:**  
Spring Boot 3 introduced native support for the IETF standard **Problem Details for HTTP APIs (RFC 7807)** via the `ProblemDetail` class. It provides a standardized JSON format for API errors (`type`, `title`, `status`, `detail`, `instance`):
```java
@ExceptionHandler(AccountNotFoundException.class)
public ProblemDetail handleAccountNotFound(AccountNotFoundException ex) {
    ProblemDetail problem = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
    problem.setTitle("Account Not Found");
    problem.setProperty("timestamp", Instant.now());
    return problem;
}
```

---

### Q31: How does Bean Validation work with `@Valid` vs `@Validated`?
**Direct Answer:**  
- `@Valid`: Standard JSR-380 Jakarta Validation annotation. Applies validation on method arguments or cascading nested objects (`@Valid Address address`). Does NOT support validation groups.
- `@Validated`: Spring's variant (`org.springframework.validation.annotation`). Can be applied at class-level for validating method parameters (e.g. `@PathVariable @Min(1) Long id`) and supports **Validation Groups** (e.g. `@Validated(OnCreate.class)` vs `@Validated(OnUpdate.class)`).
- Validation failures throw `MethodArgumentNotValidException` (body) or `ConstraintViolationException` (params).

---

### Q32: How do you write a Custom Bean Validation Annotation?
**Direct Answer:**  
1. Define the annotation:
```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PhoneNumberValidator.class)
public @interface ValidPhoneNumber {
    String message() default "Invalid phone number format";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```
2. Implement `ConstraintValidator`:
```java
public class PhoneNumberValidator implements ConstraintValidator<ValidPhoneNumber, String> {
    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value != null && value.matches("^\\+?[1-9]\\d{1,14}$");
    }
}
```

---

### Q33: Why should JPA Entities NEVER be returned directly from REST Controllers?
**Direct Answer:**  
Always map Entities to **DTOs (Data Transfer Objects)**:
1. **Security / Over-Posting:** Clients could maliciously bind internal fields (e.g. `isAdmin = true`).
2. **LazyInitializationException:** Serializing uninitialized lazy relationships outside an open transaction crashes with Jackson serialization errors.
3. **Infinite Recursion:** Bidirectional relationships (`User <-> Order`) cause `StackOverflowError` during JSON serialization.
4. **Coupling:** Database schema changes break public API contracts immediately.

---

### Q34: What is Content Negotiation in Spring Boot?
**Direct Answer:**  
Content negotiation allows a single endpoint to return different formats (JSON, XML) based on client request:
- Client sends `Accept: application/json` or `Accept: application/xml`.
- If `jackson-dataformat-xml` is on the classpath, Spring's `HttpMessageConverter` evaluates the `Accept` header and serializes into the requested media type automatically.

---

### Q35: How do you configure CORS (Cross-Origin Resource Sharing) in Spring Boot?
**Direct Answer:**  
1. **Method / Controller level:** `@CrossOrigin(origins = "https://frontend.com")`.
2. **Global level (Recommended):**
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("https://app.company.com")
            .allowedMethods("GET", "POST", "PUT", "DELETE")
            .allowedHeaders("*")
            .allowCredentials(true)
            .maxAge(3600);
    }
}
```

---

### Q36: What is the difference between a Servlet `Filter` and a `HandlerInterceptor`?
**Direct Answer:**  
- **Servlet `Filter`:** Part of the Servlet specification. Sits **outside** the `DispatcherServlet`. Intercepts raw byte streams before Spring MVC context. Ideal for security authentication, CORS, and request body logging/wrapping.
- **`HandlerInterceptor`:** Part of Spring MVC framework. Sits **inside** the `DispatcherServlet`. Has direct access to the target Spring Controller `HandlerMethod` instance. Ideal for controller-specific timing metrics, authorization checks, and view model modifications.

---

### Q37: How do you handle Asynchronous HTTP Requests in Spring Boot?
**Direct Answer:**  
Using `DeferredResult<T>` or `CompletableFuture<T>`:
```java
@GetMapping("/long-process")
public CompletableFuture<String> processAsync() {
    return CompletableFuture.supplyAsync(() -> {
        // Runs in separate thread pool, releasing Tomcat thread back to pool!
        return heavyService.execute();
    }, customExecutor);
}
```
The servlet container thread is released immediately to handle other incoming traffic while the asynchronous calculation completes in the background.

---

### Q38: How do Server-Sent Events (SSE) work in Spring Boot?
**Direct Answer:**  
SSE provides one-way streaming from server to client over standard HTTP:
```java
@GetMapping(value = "/stream-updates", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public SseEmitter streamUpdates() {
    SseEmitter emitter = new SseEmitter(60_000L); // 60s timeout
    executor.execute(() -> {
        try {
            for (int i = 0; i < 10; i++) {
                emitter.send(SseEmitter.event().data("Update #" + i));
                Thread.sleep(1000);
            }
            emitter.complete();
        } catch (Exception ex) {
            emitter.completeWithError(ex);
        }
    });
    return emitter;
}
```

---

### Q39: What are the main REST API Versioning strategies in Spring Boot?
**Direct Answer:**  
1. **URI Path (Most Popular):** `@GetMapping("/api/v1/users")`. Clear, easy caching.
2. **Query Parameter:** `@GetMapping(value = "/api/users", params = "version=1")`.
3. **Custom Request Header:** `@GetMapping(value = "/api/users", headers = "X-API-VERSION=1")`. Keeps URI clean.
4. **Media Type / Accept Header (Content Negotiation):** `@GetMapping(value = "/api/users", produces = "application/vnd.company.app-v1+json")`.

---

### Q40: How do you implement Rate Limiting in Spring Boot?
**Direct Answer:**  
Using **Bucket4j** (Token Bucket algorithm) and **Redis**:
1. Add `bucket4j-redis` dependency.
2. Intercept requests in a `HandlerInterceptor` or Servlet `Filter`:
   ```java
   Bucket bucket = redisBucketProxy.resolveBucket(clientIp);
   if (bucket.tryConsume(1)) {
       return true; // proceed
   } else {
       response.setStatus(429);
       response.setHeader("Retry-After", "60");
       return false;
   }
   ```

---

### Q41: How do you stream large file uploads in Spring Boot without blowing up heap memory?
**Direct Answer:**  
Do **NOT** use `byte[]` or load the entire `MultipartFile.getBytes()` into memory.  
**Solution:** Stream directly from the input stream to disk or AWS S3:
```java
@PostMapping("/upload")
public ResponseEntity<Void> upload(@RequestParam("file") MultipartFile file) throws IOException {
    try (InputStream is = file.getInputStream()) {
        s3Client.putObject(bucketName, key, is, file.getSize());
    }
    return ResponseEntity.ok().build();
}
```
Configure `spring.servlet.multipart.file-size-threshold=2MB` so temporary files above 2MB are spooled to disk instead of RAM.

---

### Q42: What is Spring Boot Actuator and what are its most critical endpoints?
**Direct Answer:**  
Actuator exposes production-ready operational endpoints:
- `/actuator/health`: Shows application and dependency health (DB, Redis, Disk).
- `/actuator/metrics`: JVM memory, GC pauses, HTTP request timings.
- `/actuator/env`: Environment variables and configuration properties (masked).
- `/actuator/prometheus`: Scrapes metrics in Prometheus format for Grafana.
- `/actuator/threaddump` & `/heapdump`: Diagnostic dumps.
- **Security Rule:** Restrict sensitive endpoints in production (`management.endpoints.web.exposure.include=health,metrics,prometheus`).

---

### Q43: How do you write a Custom `HealthIndicator` in Spring Boot?
**Direct Answer:**  
Implement `HealthIndicator` or extend `AbstractHealthIndicator`:
```java
@Component
public class ExternalApiHealthIndicator implements HealthIndicator {
    @Override
    public Health health() {
        boolean reachable = checkExternalService();
        if (reachable) {
            return Health.up().withDetail("service", "External Payment Gateway").build();
        }
        return Health.down().withDetail("error", "Timeout after 2000ms").build();
    }
}
```

---

### Q44: How does Graceful Shutdown work in Spring Boot 2.3+?
**Direct Answer:**  
Configured via:
```properties
server.shutdown=graceful
spring.lifecycle.timeout-per-shutdown-phase=30s
```
When SIGTERM is received (e.g. Kubernetes pod termination):
1. Tomcat stops accepting **new** incoming requests immediately.
2. Existing in-flight requests are given up to 30 seconds to complete cleanly.
3. Database pools and background workers close cleanly without aborting active transactions.

---

### Q45: How do Micrometer, Prometheus, and Grafana work together in Spring Boot?
**Direct Answer:**  
- **Micrometer:** Acts as the "SLF4J of metrics" in Spring Boot, providing vendor-neutral instrumentation (`MeterRegistry`).
- **Prometheus:** Periodically scrapes the `/actuator/prometheus` endpoint, pulling metrics data into its time-series database.
- **Grafana:** Visualizes the time-series data stored in Prometheus via dashboards (p99 latency, error rates, GC duration).

---

### Q46: How do you record Custom Metrics using Micrometer?
**Direct Answer:**  
Inject `MeterRegistry`:
```java
@Service
public class OrderService {
    private final Counter orderCounter;
    private final Timer orderTimer;

    public OrderService(MeterRegistry registry) {
        this.orderCounter = Counter.builder("orders.placed")
            .tag("region", "US-EAST")
            .register(registry);
        this.orderTimer = registry.timer("orders.duration");
    }

    public void placeOrder() {
        orderTimer.record(() -> {
            // business logic
            orderCounter.increment();
        });
    }
}
```

---

### Q47: How does Distributed Tracing work with Micrometer Tracing / OpenTelemetry?
**Direct Answer:**  
1. When an HTTP request enters an API Gateway, Spring generates a **Trace ID** (unique per end-to-end request) and a **Span ID** (unique per hop/microservice).
2. The Trace ID is injected into MDC logging context and propagated across downstream services via W3C Trace Context headers (`traceparent`).
3. Logs aggregated in OpenSearch/Datadog can be filtered by `traceId` to view the exact latency breakdown across 10 different microservices.

---

### Q48: How do you tune Embedded Tomcat thread pools for high QPS?
**Direct Answer:**  
Configured in `application.yml`:
```yaml
server:
  tomcat:
    threads:
      max: 400         # Maximum worker threads (default 200)
      min-spare: 50    # Minimum idle threads ready
    accept-count: 200  # OS TCP connection queue backlog when all worker threads are busy
    max-connections: 10000 # Max simultaneous TCP sockets
```
If `threads.max` is exhausted and `accept-count` is full, incoming connections are rejected with "Connection Refused".

---

### Q49: What is `OpenEntityManagerInView` (OSIV) and why should it be disabled in production?
**Direct Answer:**  
`spring.jpa.open-in-view=true` (enabled by default) keeps the Hibernate Session/EntityManager open throughout the entire web request, including during view rendering and JSON serialization.  
**Why it is dangerous:**
1. A database connection is held checked out from HikariCP for the entire HTTP request duration (including slow network transfers!).
2. Hidden N+1 queries execute silently during Jackson JSON serialization.  
**Production Rule:** Always set `spring.jpa.open-in-view=false`.

---

### Q50: How do you enable HTTP/2 in Spring Boot?
**Direct Answer:**  
1. HTTP/2 requires TLS (SSL):
```yaml
server:
  port: 8443
  http2:
    enabled: true
  ssl:
    key-store: classpath:keystore.p12
    key-store-password: secretpassword
    key-store-type: PKCS12
```
Enables multiplexing multiple HTTP requests over a single TCP connection, header compression (HPACK), and server push.
