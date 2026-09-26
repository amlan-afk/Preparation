# 🍃 Spring Boot: Core IoC, DI, Bean Lifecycle & Configuration (Q1 – Q25)

---

### Q1: What is Spring Boot and how does it differ from the traditional Spring Framework?
**Direct Answer:**  
Spring Framework is a comprehensive enterprise Java framework providing IoC, DI, AOP, and transaction management, but historically required heavy XML or manual Java `@Configuration` and WAR deployments.  
**Spring Boot** is an opinionated, convention-over-configuration layer on top of Spring:
1. **Auto-Configuration:** Automatically configures beans based on classpath dependencies.
2. **Starter Dependencies:** Curated POM dependencies (`spring-boot-starter-web`) resolving transitive library versions.
3. **Embedded Web Servers:** Bundles embedded Tomcat/Jetty/Undertow, compiling to a standalone runnable fat JAR (`java -jar app.jar`).
4. **Production-Ready Metrics:** Built-in health checks and metrics via Spring Boot Actuator.

---

### Q2: What annotations make up `@SpringBootApplication`?
**Direct Answer:**  
`@SpringBootApplication` is a meta-annotation that bundles:
1. `@SpringBootConfiguration`: Marks the class as a source of bean definitions (specialized `@Configuration`).
2. `@EnableAutoConfiguration`: Enables Spring Boot's auto-configuration mechanism to guess and configure beans based on classpath JARs.
3. `@ComponentScan`: Scans for components (`@Component`, `@Service`, `@Repository`, `@Controller`) in the current package and all sub-packages.

---

### Q3: How does Spring Boot Auto-Configuration work under the hood?
**Direct Answer:**  
1. At startup, `@EnableAutoConfiguration` imports `AutoConfigurationImportSelector`.
2. It reads `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (in Spring Boot 3+) or `spring.factories` (in Spring Boot 2.x).
3. Each candidate auto-configuration class evaluates **conditional annotations**:
   - `@ConditionalOnClass(DataSource.class)`: Run only if DataSource is on classpath.
   - `@ConditionalOnMissingBean(DataSource.class)`: Run only if the developer has NOT declared their own custom DataSource bean.
   - `@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")`.
4. Only configuration classes whose conditions evaluate to `true` register their beans in the `ApplicationContext`.

---

### Q4: What is Inversion of Control (IoC) and Dependency Injection (DI)?
**Direct Answer:**  
- **IoC (Inversion of Control):** An architectural design principle where control of object creation, configuration, and lifecycle is inverted from the application code to an external container/framework (the Spring IoC Container).
- **DI (Dependency Injection):** The specific design pattern used to implement IoC. Instead of an object instantiating its own dependencies (`new OrderRepository()`), the container injects dependent objects at runtime via constructors, setters, or fields.

---

### Q5: Why is Constructor Injection strongly preferred over Field Injection?
**Direct Answer:**  
Field injection (`@Autowired private OrderService orderService;`) is considered an anti-pattern:
1. **Immutability:** Dependencies cannot be marked `final`.
2. **Null Safety / Testing:** Objects cannot be instantiated in unit tests without reflection or Spring test runners, leading to `NullPointerExceptions`.
3. **Circular Dependencies:** Hides circular dependencies until runtime, whereas constructor injection fails fast at startup.
4. **SRP Violations:** It is easy to inject 10+ dependencies via fields without noticing, whereas a constructor with 10 parameters immediately signals a code smell.

---

### Q6: What are the Bean Scopes in Spring Boot?
**Direct Answer:**  
1. **singleton (Default):** Exactly one instance per Spring IoC container. Stateless, thread-safe.
2. **prototype:** A new instance is created every time the bean is requested from the container.
3. **request (Web):** One instance per HTTP request lifecycle.
4. **session (Web):** One instance per HTTP session lifecycle.
5. **application (Web):** One instance per `ServletContext`.
6. **websocket (Web):** One instance per WebSocket lifecycle.

---

### Q7: What happens when a `prototype` bean is injected into a `singleton` bean? How do you fix it?
**Direct Answer:**  
**The Problem:** The singleton bean is initialized only once at startup. Consequently, the prototype bean is also injected only once! Subsequent calls to the singleton bean reuse the exact same prototype instance, defeating the prototype scope.  
**Solutions:**
1. **Method Injection via `@Lookup`:** Spring subclasses the singleton using CGLIB and dynamically looks up a fresh prototype bean on every method call:
   ```java
   @Lookup
   public abstract PrototypeBean getPrototypeBean();
   ```
2. **`ObjectProvider<T>` or `Provider<T>`:** Call `prototypeProvider.getObject()` when needed.

---

### Q8: Describe the complete lifecycle of a Spring Bean.
**Direct Answer:**  
1. **Instantiation:** Constructor invocation / reflection.
2. **Populate Properties:** Dependency injection of properties and references.
3. **Aware Interfaces:** Invokes `BeanNameAware`, `BeanFactoryAware`, `ApplicationContextAware`.
4. **BeanPostProcessor (Before):** `postProcessBeforeInitialization()`.
5. **Initialization:**
   - Methods annotated with `@PostConstruct`.
   - `InitializingBean.afterPropertiesSet()`.
   - Custom `init-method` declared in `@Bean(initMethod = "init")`.
6. **BeanPostProcessor (After):** `postProcessAfterInitialization()` (AOP proxies like `@Transactional` are created here!).
7. **Ready to Use:** Bean is active in container.
8. **Destruction:**
   - Methods annotated with `@PreDestroy`.
   - `DisposableBean.destroy()`.
   - Custom `destroyMethod`.

---

### Q9: What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?
**Direct Answer:**  
All four are stereotypes annotated with `@Component`, making them auto-detectable by `@ComponentScan`:
- `@Component`: Generic stereotype for any Spring-managed component.
- `@Service`: Marks business logic layer. No special semantic behavior; used for architectural clarity.
- `@Repository`: Marks data access layer. Enables automatic **exception translation**: translates low-level database exceptions (e.g. `SQLException`, `HibernateException`) into Spring's unified `DataAccessException` hierarchy.
- `@Controller` / `@RestController`: Marks presentation/web layer handling incoming HTTP requests.

---

### Q10: Why does `@Configuration` use CGLIB proxying? What happens if you define beans in `@Component`?
**Direct Answer:**  
`@Configuration(proxyBeanMethods = true)` instructs Spring to wrap the configuration class in a **CGLIB dynamic proxy**:
- When one `@Bean` method calls another `@Bean` method within the same configuration class, the CGLIB proxy intercepts the call, checks if the singleton instance already exists in the container, and returns the cached bean instead of instantiating a second object.
- In `@Component` (or `@Configuration(proxyBeanMethods = false)`), inter-bean method calls are standard Java method calls that execute `new Object()`, creating redundant duplicate instances.

---

### Q11: How do `@Primary` and `@Qualifier` resolve ambiguous dependency injection?
**Direct Answer:**  
When an interface has multiple implementing beans (e.g. `PaypalService` and `StripeService` implementing `PaymentService`):
- `@Primary`: Designates one bean as the default choice when no specific qualifier is requested.
- `@Qualifier("stripeService")`: Explicitly specifies the exact bean name to inject at the injection point, overriding `@Primary`.

---

### Q12: What is the difference between `@Value` and `@ConfigurationProperties`?
**Direct Answer:**  
- `@Value("${app.timeout}")`: Evaluates SpEL, binds individual primitive values. No type-safety, no relaxed binding, no validation.
- `@ConfigurationProperties(prefix = "app")`: Binds hierarchical properties to a structured POJO or record. Supports **type-safety**, **relaxed binding** (`app.read-timeout`, `app.readTimeout`, `app_read_timeout` all bind to `readTimeout`), and **JSR-380 validation** (`@NotNull`, `@Min`).

---

### Q13: What is the order of precedence for Spring Boot configuration sources?
**Direct Answer:**  
From highest to lowest (overrides from top down):
1. Command-line arguments (`--server.port=9090`).
2. Java System properties (`-Dserver.port=9090`).
3. OS Environment variables (`SERVER_PORT=9090`).
4. Profile-specific application properties outside packaged jar (`config/application-{profile}.properties`).
5. Profile-specific application properties inside packaged jar (`application-{profile}.properties`).
6. Application properties outside packaged jar (`config/application.properties`).
7. Application properties inside packaged jar (`application.properties`).
8. `@PropertySource` annotations.
9. Default properties (`SpringApplication.setDefaultProperties`).

---

### Q14: How does Spring Boot detect and prevent Circular Dependencies?
**Direct Answer:**  
**Cause:** Bean A requires Bean B in constructor, and Bean B requires Bean A.  
**Detection:** Spring tracks beans currently under creation in a `Set<String> singletonsCurrentlyInCreation`. If a bean is requested that is already present in this set, Spring throws `BeanCurrentlyInCreationException`.  
**Solutions:**
1. **Best Practice:** Redesign code to eliminate cyclic coupling (introduce a third mediating service or event publisher).
2. **`@Lazy`:** `@Autowired public BeanA(@Lazy BeanB b)` injects a dynamic proxy; the real instance is loaded only when first invoked.
3. *Note: Spring Boot 2.6+ disables circular dependencies by default (`spring.main.allow-circular-references=false`).*

---

### Q15: What is Spring AOP and what are its key concepts?
**Direct Answer:**  
Aspect-Oriented Programming (AOP) modularizes cross-cutting concerns (logging, security, transactions):
- **Aspect:** Module containing cross-cutting logic (`@Aspect`).
- **Join Point:** A point during program execution (in Spring AOP, always a method execution).
- **Pointcut:** Expression matching join points (`execution(* com.service.*.*(..))`).
- **Advice:** Action taken at a join point (`@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around`). `@Around` is the most powerful because it controls whether the target method executes via `ProceedingJoinPoint.proceed()`.

---

### Q16: How does Spring implement AOP proxies: JDK Dynamic Proxies vs CGLIB?
**Direct Answer:**  
- **JDK Dynamic Proxy:** Created using `java.lang.reflect.Proxy`. Requires the target class to implement an **interface**. Proxies only interface methods.
- **CGLIB Proxy:** Generates dynamic bytecode subclasses at runtime. Can proxy concrete classes without interfaces. Cannot override `final` classes or `final` methods.
- **Spring Boot Default:** Since Spring Boot 2.x, **CGLIB is the default proxy mechanism** (`spring.aop.proxy-target-class=true`).

---

### Q17: What is the difference between `BeanFactory` and `ApplicationContext`?
**Direct Answer:**  
- `BeanFactory`: Basic IoC container interface. Provides lazy bean loading (beans created only when `getBean()` is called). Minimal footprint; used in memory-constrained environments.
- `ApplicationContext`: Extends `BeanFactory`. Provides eager pre-instantiation of singleton beans, internationalization (`MessageSource`), event publication (`ApplicationEventPublisher`), AOP integration, and environment profile resolution. Always used in modern Spring Boot apps.

---

### Q18: What is the difference between `CommandLineRunner` and `ApplicationRunner`?
**Direct Answer:**  
Both are functional interfaces whose `run()` method executes once after the `ApplicationContext` is fully loaded and before `SpringApplication.run()` completes.  
- `CommandLineRunner`: `run(String... args)` accepts raw string array arguments.
- `ApplicationRunner`: `run(ApplicationArguments args)` provides structured access to parsed option arguments (`--key=value`) and non-option arguments.

---

### Q19: How do Spring Application Events work (`ApplicationEventPublisher`)?
**Direct Answer:**  
Enables decoupled event-driven communication inside the JVM:
```java
// 1. Publish
@Autowired private ApplicationEventPublisher publisher;
publisher.publishEvent(new UserRegisteredEvent(userId));

// 2. Consume
@EventListener
public void onUserRegistered(UserRegisteredEvent event) {
    sendWelcomeEmail(event.userId());
}
```
Use `@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)` to guarantee the event handler runs only after the enclosing database transaction successfully commits.

---

### Q20: What is Lazy Initialization in Spring Boot and what are its trade-offs?
**Direct Answer:**  
Enabled via `spring.main.lazy-initialization=true`. Beans are instantiated on-demand when requested rather than eagerly at startup.  
- **Pros:** Significantly faster local startup time and reduced test execution time.
- **Cons:** Errors (missing dependencies, invalid configs) are deferred to runtime when traffic hits the bean. First HTTP request experiences high latency penalty.

---

### Q21: What are Conditional Annotations and how do they work?
**Direct Answer:**  
Annotations that evaluate boolean predicates before registering a bean:
- `@ConditionalOnProperty(name = "cache.type", havingValue = "redis")`
- `@ConditionalOnClass(name = "com.mongodb.client.MongoClient")`
- `@ConditionalOnMissingBean(ObjectMapper.class)`
- `@ConditionalOnExpression("#{systemProperties['env'] == 'prod'}")`
Custom conditions implement the `Condition` interface: `boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata)`.

---

### Q22: How does Spring Boot run without an external Tomcat server?
**Direct Answer:**  
Spring Boot embeds the servlet container (Tomcat by default via `spring-boot-starter-tomcat`) directly as a library JAR in the fat executable archive. At startup, `ServletWebServerApplicationContext` programmatically initializes `Tomcat`, binds the listening port (8080), registers the `DispatcherServlet`, and starts the connector thread pool.

---

### Q23: What is Spring Native and GraalVM Ahead-Of-Time (AOT) compilation?
**Direct Answer:**  
Traditional JVM Spring Boot apps start in seconds and require 200MB+ RAM because they perform runtime reflection and dynamic proxy generation.  
**Spring Boot 3 + GraalVM Native Image:** Pre-computes reflection, proxies, and bean registrations at build-time (AOT). Compiles Java bytecode into a native binary executable:
- Startup time: **$< 50\text{ milliseconds}$**.
- Memory footprint: **$< 30\text{ MB}$**. Ideal for serverless (AWS Lambda) and scale-to-zero container deployments.

---

### Q24: How do you create a Custom Spring Boot Starter?
**Direct Answer:**  
A custom starter consists of two modules:
1. `my-feature-spring-boot-autoconfigure`: Contains auto-configuration classes, conditional bean declarations (`@ConditionalOnMissingBean`), and properties POJOs (`@ConfigurationProperties`).
2. `my-feature-spring-boot-starter`: Empty POM aggregator declaring dependencies on the autoconfigure module and any core third-party libraries.
3. Register the configuration in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.

---

### Q25: How do you gracefully reload configuration properties in Spring Boot without restarting?
**Direct Answer:**  
Using **Spring Cloud Config** or **Spring Boot Actuator**:
1. Annotate the configuration bean or consumer service with `@RefreshScope`.
2. Update external configuration (e.g. Git repository or Consul).
3. Trigger a `POST /actuator/refresh` endpoint. Spring recreates the `@RefreshScope` beans with updated property values on their next access.
