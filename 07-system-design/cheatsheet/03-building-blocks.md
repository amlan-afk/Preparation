# 🧱 System Design Building Blocks Cheat Sheet

A concise reference for the fundamental architectural building blocks used in system design interviews.

---

## 1. Load Balancing & Reverse Proxies

- **Layer 4 (Transport - TCP/UDP):** Extremely fast, forwards raw packets based on IP + Port (e.g. AWS NLB, HAProxy). No inspection of HTTP headers/cookies.
- **Layer 7 (Application - HTTP/HTTPS):** Inspects headers, paths, cookies. Enables path-based routing (`/api/v1/users`), SSL termination, sticky sessions (e.g. AWS ALB, NGINX, Envoy).
- **Algorithms:** Round Robin, Weighted Round Robin, Least Connections, Consistent Hashing (for sticky state).

---

## 2. Caching Strategies & Placement

### Caching Patterns:
1. **Cache-Aside (Lazy Loading):**
   - App queries Cache $\rightarrow$ Miss $\rightarrow$ Queries DB $\rightarrow$ Writes to Cache.
   - *Pros:* Only requested data is cached. Resilient to cache crashes.
   - *Cons:* Cache miss penalty on first read; data can become stale if DB is updated directly.
2. **Read-Through:**
   - App treats cache as the sole data store; cache library loads from DB on miss.
3. **Write-Through:**
   - App writes to Cache $\rightarrow$ Cache synchronously writes to DB.
   - *Pros:* Data in cache is never stale.
   - *Cons:* Higher write latency (two writes).
4. **Write-Behind (Write-Back):**
   - App writes to Cache $\rightarrow$ Cache acknowledges immediately $\rightarrow$ Asynchronously flushes to DB in batches.
   - *Pros:* Blazing fast writes.
   - *Cons:* Risk of data loss if cache crashes before flushing to disk.

### Cache Eviction Policies:
- **LRU (Least Recently Used):** Evicts item not accessed for the longest time (Doubly Linked List + Hash Map).
- **LFU (Least Frequently Used):** Evicts item with lowest access counter.
- **TTL (Time To Live):** Evicts when expiration window passes.

### Cache Failure Patterns:
- **Cache Stampede (Thundering Herd):** High-traffic key expires $\rightarrow$ Thousands of concurrent requests hit DB simultaneously.
  - *Fix:* Distributed mutex / locking; early refresh before TTL expiration; probabilistic early expiration (XFetch).
- **Cache Penetration:** Requests query keys that don't exist in DB $\rightarrow$ Every request bypasses cache to DB.
  - *Fix:* Bloom Filter in front of cache; cache `null` with short TTL.
- **Cache Avalanche:** Many keys expire at the exact same moment.
  - *Fix:* Add random jitter to TTLs (e.g. `base_ttl + rand(0, 300)`).

---

## 3. Database Paradigms (SQL vs NoSQL)

| Feature | Relational (SQL) | Document (MongoDB) | Key-Value (Redis/DynamoDB) | Wide-Column (Cassandra) |
|---|---|---|---|---|
| **Data Schema** | Strict tabular schema | Flexible JSON/BSON | Key -> Blob/JSON | Column families |
| **Transactions** | Strong ACID | Single-document ACID | Single-key atomicity | Eventual consistency |
| **Best For** | Financial, relational data | Fast evolving schema | Fast lookups, sessions | High write volume, time-series |
| **Scaling** | Vertical, Read Replicas, Sharding | Horizontal (Sharding) | Horizontal (Hash slot) | Masterless horizontal ring |

---

## 4. Message Queues & Streaming

- **RabbitMQ / SQS (Point-to-Point Queue):**
  - Message is consumed by one worker and deleted.
  - Best for task offloading (image processing, email sending).
- **Kafka / AWS Kinesis (Distributed Append-Only Commit Log):**
  - Messages are partitioned into ordered logs with retention windows.
  - Multiple consumer groups can independently read from their own offset.
  - Best for event-driven architectures, real-time analytics, metrics pipelines.

---

## 5. CAP Theorem & PACELC

- **CAP Theorem:** In the presence of a Network Partition (P), you must choose between:
  - **Consistency (CP):** Return latest data or error out (e.g. HBase, Zookeeper, Redis Sentinel).
  - **Availability (AP):** Always return a response, even if stale (e.g. Cassandra, DynamoDB, CouchDB).
- **PACELC Theorem:**
  - If **P**artition: Choose between **A**vailability and **C**onsistency.
  - **E**lse: Choose between **L**atency and **C**onsistency.
