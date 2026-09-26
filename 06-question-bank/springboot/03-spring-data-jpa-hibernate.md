# 🍃 Spring Boot: Data Access, JPA, Hibernate & Caching (Q51 – Q75)

---

### Q51: How do Spring Data JPA, Hibernate, and JDBC relate to each other?
**Direct Answer:**  
- **JDBC (Java Database Connectivity):** Low-level Java standard API for executing raw SQL queries via drivers. Requires manual connection handling, statement execution, and mapping `ResultSet` rows.
- **Hibernate:** A full-featured **ORM (Object-Relational Mapping)** framework that implements the JPA specification. Automatically maps Java objects to relational tables, generates SQL queries, and manages dirty checking and caching.
- **Spring Data JPA:** An abstraction layer on top of JPA/Hibernate. Eliminates boilerplate DAO implementations by generating repository proxies at runtime (`JpaRepository`) and standardizing paging and querying.

---

### Q52: What is the hierarchy of Repository interfaces in Spring Data?
**Direct Answer:**  
1. `Repository<T, ID>`: Root marker interface (no methods).
2. `CrudRepository<T, ID>`: Provides basic CRUD operations (`save`, `findById`, `existsById`, `deleteById`).
3. `PagingAndSortingRepository<T, ID>`: Adds sorting and pagination (`findAll(Pageable)`, `findAll(Sort)`).
4. `JpaRepository<T, ID>`: Full JPA-specific extensions (`flush()`, `saveAndFlush()`, `deleteAllInBatch()`, returns `List` instead of `Iterable`).

---

### Q53: What is the N+1 Query Problem in JPA and how do you resolve it?
**Direct Answer:**  
**The Problem:** Querying $N$ parent entities (e.g. 100 Orders) causes Hibernate to execute 1 query for the parents, plus **$N$ separate queries** to fetch each parent's associated children (e.g. Items), resulting in $1 + 100 = 101\text{ queries}$!  
**Solutions:**
1. **`JOIN FETCH` in JPQL (Most Common):**
   ```sql
   @Query("SELECT o FROM Order o JOIN FETCH o.items")
   List<Order> findAllOrdersWithItems();
   ```
2. **`@EntityGraph`:**
   ```java
   @EntityGraph(attributePaths = {"items"})
   List<Order> findAll();
   ```
3. **Batch Fetching (Global Fix):** Set `spring.jpa.properties.hibernate.default_batch_fetch_size=30`. Hibernate queries children using `WHERE order_id IN (?, ?, ...)`, turning 100 queries into just 4!

---

### Q54: What causes `LazyInitializationException` and how do you resolve it?
**Direct Answer:**  
**Cause:** Attempting to access an uninitialized lazy association (`order.getItems().size()`) after the Hibernate `Session` / `EntityManager` has closed (typically outside a `@Transactional` boundary).  
**Solutions:**
1. Fetch the required data while the session is still open using `JOIN FETCH` or `@EntityGraph`.
2. Map to a DTO with the required fields directly within the `@Transactional` service layer.
3. **Anti-pattern to avoid:** Do NOT enable `OpenSessionInView` to fix this!

---

### Q55: What is the difference between First-Level and Second-Level Cache in Hibernate?
**Direct Answer:**  
- **First-Level Cache (L1):** Enabled by default and bound to the current Hibernate `Session` / transaction. Caches entity instances by ID. Multiple reads for the same entity in the same transaction return the exact same object reference without touching the DB. Cleared when session closes.
- **Second-Level Cache (L2):** Optional, shared across all sessions/transactions across the entire application (or cluster). Backed by Redis, Ehcache, or Hazelcast. Caches entity state across transactions.

---

### Q56: How does the `@Transactional` annotation work under the hood?
**Direct Answer:**  
`@Transactional` is implemented via **Spring AOP CGLIB Proxies**:
1. When a client calls a transactional method, the proxy intercepts the call.
2. The proxy opens a database connection via `PlatformTransactionManager` and begins the transaction (`connection.setAutoCommit(false)`).
3. It binds the connection to the current thread via `TransactionSynchronizationManager`.
4. It executes the target method inside a `try` block.
5. If the method completes normally, the proxy calls `connection.commit()`.
6. If an unchecked exception (`RuntimeException` or `Error`) is thrown, it calls `connection.rollback()`.

---

### Q57: Why does `@Transactional` NOT roll back on checked exceptions by default?
**Direct Answer:**  
By default, Spring rolls back transactions **only for unchecked exceptions (`RuntimeException` and `Error`)**. Checked exceptions (`Exception`) are considered business recoverable conditions.  
**To roll back on all exceptions:**
```java
@Transactional(rollbackFor = Exception.class)
```

---

### Q58: Why does `@Transactional` fail when called from another method within the same class?
**Direct Answer:**  
**The Self-Invocation Problem:** Spring AOP proxies intercept calls made **from outside the bean**. When Method A calls Method B within the same class:
```java
public void methodA() {
    this.methodB(); // Direct 'this' call bypasses the CGLIB proxy!
}
@Transactional
public void methodB() { ... }
```
The call bypasses the proxy entirely; no transaction interceptor executes, and `methodB` runs non-transactionally!  
**Fix:** Move `methodB` to a separate service bean, or self-inject the bean (`@Autowired private MyService self`).

---

### Q59: What are the Spring Transaction Propagation levels?
**Direct Answer:**  
- `REQUIRED` (Default): Joins the existing transaction if one exists; creates a new one if none exists.
- `REQUIRES_NEW`: Always suspends the existing transaction and opens a brand-new independent physical transaction.
- `NESTED`: Executes within a nested transaction using savepoints if a transaction exists.
- `SUPPORTS`: Executes non-transactionally if none exists; joins if one exists.
- `MANDATORY`: Requires an existing transaction; throws `TransactionRequiredException` if none exists.
- `NOT_SUPPORTED`: Suspends existing transaction and runs non-transactionally.
- `NEVER`: Throws exception if an active transaction exists.

---

### Q60: What are the Transaction Isolation levels and what anomalies do they prevent?
**Direct Answer:**  
1. **READ_UNCOMMITTED:** Lowest level. Allows **Dirty Reads** (reading uncommitted data).
2. **READ_COMMITTED:** Prevents dirty reads. Allows **Non-Repeatable Reads** (re-reading same row returns different values because another transaction committed an update).
3. **REPEATABLE_READ:** Prevents dirty reads and non-repeatable reads. Uses MVCC locks. May allow **Phantom Reads** (new rows inserted by another transaction).
4. **SERIALIZABLE:** Strict full serialization. Highest isolation, slowest performance.

---

### Q61: What is the difference between Optimistic Locking and Pessimistic Locking?
**Direct Answer:**  
- **Optimistic Locking:** Assumes conflicts are rare. Uses a `@Version` field (integer or timestamp) on the entity:
  ```sql
  UPDATE account SET balance = 50, version = version + 1 WHERE id = 1 AND version = 2;
  ```
  If another transaction updated the row first, `UPDATE` returns 0 affected rows and Spring throws `OptimisticLockingFailureException`.
- **Pessimistic Locking:** Assumes conflicts are frequent. Acquires a database-level lock immediately:
  ```java
  @Lock(LockModeType.PESSIMISTIC_WRITE)
  @Query("SELECT a FROM Account a WHERE a.id = :id")
  Account findByIdForUpdate(@Param("id") Long id); // SELECT ... FOR UPDATE
  ```

---

### Q62: What is the difference between `orphanRemoval = true` and `CascadeType.REMOVE`?
**Direct Answer:**  
- `CascadeType.REMOVE`: If the **parent entity is deleted**, all its child entities are automatically deleted from the database.
- `orphanRemoval = true`: If a child entity is **removed from the parent's collection** (`order.getItems().remove(item)`), Hibernate detects that the child has been orphaned and automatically issues a `DELETE` statement for that child!

---

### Q63: Describe the four Entity Lifecycle states in JPA.
**Direct Answer:**  
1. **Transient (New):** Just instantiated (`new User()`). Has no primary key, not associated with an EntityManager session, not in DB.
2. **Persistent (Managed):** Associated with an active EntityManager session. Has a DB identifier. Any property changes are automatically tracked and synchronized to DB at flush time (**Dirty Checking**).
3. **Detached:** Was once persistent, but the session closed or `em.detach(entity)` was called. Changes are not tracked unless re-merged via `em.merge()`.
4. **Removed:** Scheduled for deletion upon transaction commit (`em.remove(entity)`).

---

### Q64: How does Hibernate Dirty Checking work?
**Direct Answer:**  
When an entity enters the Managed state, Hibernate takes a snapshot of its initial state in the L1 Session cache. During transaction commit or `em.flush()`, Hibernate compares the current state of the entity's properties against the initial snapshot. If differences are detected, it automatically generates and executes an optimized SQL `UPDATE` statement without requiring explicit `repository.save()` calls!

---

### Q65: How do you configure and size the HikariCP connection pool in Spring Boot?
**Direct Answer:**  
HikariCP is the default connection pool in Spring Boot.  
**Formula for Pool Size (PostgreSQL Wiki):**
$$\text{pool\_size} = (\text{core\_count} \times 2) + \text{effective\_spindle\_count}$$
For an 8-core CPU server: $8 \times 2 + 1 = 17\text{ connections}$. Setting pool size to 500 degrades throughput due to CPU context switching and disk thrashing.  
**Configuration:**
```yaml
spring.datasource.hikari:
  maximum-pool-size: 20
  minimum-idle: 10
  idle-timeout: 300000
  connection-timeout: 20000       # 20s wait before failing
  leak-detection-threshold: 2000  # Logs warning if connection checked out > 2s
```

---

### Q66: What is the difference between Flyway and Liquibase?
**Direct Answer:**  
Both are database migration tools that version database schemas:
- **Flyway:** SQL-first. Migrations are written in plain SQL files (`V1__init.sql`, `V2__add_index.sql`). Very simple, easy to read, uses native DB features.
- **Liquibase:** Format-agnostic (XML, YAML, JSON, or SQL). Supports automatic database rollbacks and multi-database dialect abstraction.

---

### Q67: How do you implement Read/Write Database Splitting in Spring Boot?
**Direct Answer:**  
Using `AbstractRoutingDataSource`:
1. Define two DataSources: Primary (Writer) and Replica (Reader).
2. Create a custom `RoutingDataSource extends AbstractRoutingDataSource` that inspects `TransactionSynchronizationManager.isCurrentTransactionReadOnly()`.
3. If `@Transactional(readOnly = true)`, route connection to Replica. Otherwise, route to Primary.

---

### Q68: How do Spring Caching annotations work (`@Cacheable`, `@CachePut`, `@CacheEvict`)?
**Direct Answer:**  
Backed by a `CacheManager` (e.g. `RedisCacheManager`):
- `@Cacheable(value = "users", key = "#id")`: Checks cache first. If found, returns cached value without executing method. If miss, executes method and stores result in cache.
- `@CachePut(value = "users", key = "#user.id")`: Always executes the method and updates the cache with the new return value.
- `@CacheEvict(value = "users", key = "#id")`: Removes the specified key from cache. Use `allEntries = true` to purge entire cache.

---

### Q69: Why do bulk updates in Spring Data JPA require `@Modifying` and `clearAutomatically = true`?
**Direct Answer:**  
```java
@Modifying(clearAutomatically = true)
@Query("UPDATE User u SET u.active = false WHERE u.lastLogin < :cutoff")
int deactivateInactiveUsers(@Param("cutoff") Instant cutoff);
```
- `@Modifying` tells Spring that this query mutates the database (issues `executeUpdate()` instead of `executeQuery()`).
- **`clearAutomatically = true`:** Bulk updates bypass the Hibernate L1 Persistence Context and execute directly against the DB. Without `clearAutomatically = true`, the L1 cache retains stale entity snapshots in memory, leading to desynchronization!

---

### Q70: How do JPA Specifications implement dynamic multi-criteria search queries?
**Direct Answer:**  
`JpaSpecificationExecutor<T>` uses the JPA Criteria API to build dynamic `WHERE` clauses at runtime:
```java
public static Specification<Product> hasCategory(String cat) {
    return (root, query, cb) -> cat == null ? null : cb.equal(root.get("category"), cat);
}
// Combine dynamically:
Specification<Product> spec = Specification.where(hasCategory("Electronics")).and(priceLessThan(500));
List<Product> products = productRepo.findAll(spec);
```

---

### Q71: What is the difference between `save()` and `saveAndFlush()` in Spring Data JPA?
**Direct Answer:**  
- `save(entity)`: Saves entity to the Hibernate L1 session memory. The SQL `INSERT`/`UPDATE` is **delayed until the transaction commits** or flush is triggered (enables write-behind batching).
- `saveAndFlush(entity)`: Saves and immediately forces Hibernate to flush all pending SQL changes to the database within the current transaction. Useful when an immediate native query or external trigger needs to see the changes.

---

### Q72: How does Auditing work in Spring Data JPA?
**Direct Answer:**  
1. Enable auditing: `@EnableJpaAuditing(auditorAwareRef = "auditorProvider")`.
2. Add `@EntityListeners(AuditingEntityListener.class)` to entities.
3. Annotate fields:
   - `@CreatedDate private Instant createdAt;`
   - `@LastModifiedDate private Instant updatedAt;`
   - `@CreatedBy private String createdBy;`
   - `@LastModifiedBy private String updatedBy;`
4. Implement `AuditorAware<String>` to pull the current user from Spring Security's `SecurityContextHolder`.

---

### Q73: What is the difference between `@Embedded` and `@Embeddable` in JPA?
**Direct Answer:**  
- `@Embeddable`: Placed on a class to declare that its properties should be stored in the same database table as its owning entity (Value Object / Component pattern).
- `@Embedded`: Placed on the field in the entity class that uses the embeddable class.
- Override column mappings using `@AttributeOverride`.

---

### Q74: How do you implement Database Multi-Tenancy in Spring Data JPA?
**Direct Answer:**  
Three common patterns:
1. **Database-per-Tenant:** Each tenant has an independent database instance. Most secure, highest operational cost.
2. **Schema-per-Tenant:** Shared database instance; different schema per tenant.
3. **Shared Database, Shared Schema (Discriminator Column):** Every table has a `tenant_id` column. Uses Hibernate `@TenantId` or Hibernate Filter to automatically append `WHERE tenant_id = :currentTenant` to all generated queries.

---

### Q75: How does Hibernate handle primary key generation: `IDENTITY` vs `SEQUENCE`?
**Direct Answer:**  
- `GenerationType.IDENTITY`: Relies on DB auto-increment column (e.g. MySQL `AUTO_INCREMENT`). **Disables Hibernate JDBC batch inserts** because Hibernate must execute the `INSERT` immediately to get the generated ID!
- `GenerationType.SEQUENCE`: Uses a database sequence object (PostgreSQL/Oracle). Allows Hibernate to pre-allocate IDs in batches (allocation size default 50), enabling high-performance **JDBC batch inserts**!
