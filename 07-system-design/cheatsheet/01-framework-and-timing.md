# ⏱️ System Design Interview Framework & 45-Minute Timing Strategy

The most common failure mode in SDE 2 / Senior system design interviews is poor time allocation (e.g., spending 25 minutes calculating storage or jumping straight to drawing databases without agreeing on scope).

Follow this battle-tested 5-phase protocol.

---

## ⏳ 45-Minute Time Allocation Matrix

```
[00:00 - 05:00]  Phase 1: Clarification & Scope (Requirements Gathering)
[05:00 - 10:00]  Phase 2: Scale & Back-of-the-Envelope Calculations
[10:00 - 25:00]  Phase 3: High-Level Architecture & End-to-End Flow
[25:00 - 38:00]  Phase 4: Component Deep Dives & Core Bottlenecks
[38:00 - 45:00]  Phase 5: Bottlenecks, Fault Tolerance & Next Steps
```

---

## 📌 Phase 1: Clarification & Requirements Gathering (0 – 5 min)

> **Rule:** Never start designing until you and the interviewer have written down 3-4 core functional requirements and 3 non-functional requirements.

### 1. Functional Requirements (What does the system actually do?)
- Limit to **top 3 to 4 core features**.
- Explicitly state what is **out of scope**:
  - *Example (Twitter):* "In-scope: Post a tweet, follow users, view home timeline. Out-of-scope: Direct messages, ads, search filtering."

### 2. Non-Functional Requirements (System Quality Attributes)
- **High Availability vs Strong Consistency:** (CAP theorem trade-off: Is eventual consistency acceptable?)
- **Latency Targets:** (e.g., Read latency P99 < 100ms, Write latency P99 < 500ms)
- **Scale:** (Number of Daily Active Users, read/write ratio)
- **Durability:** (Can data ever be lost? Financial vs social feed)

---

## 📌 Phase 2: Capacity Estimation & Scale (5 – 10 min)

Keep numbers simple and round to powers of 10. Avoid overcomplicating.

1. **Traffic Estimation:**
   - Active Users: e.g., $100\text{M DAU}$
   - Reads: $100\text{M} \times 10 = 1\text{B reads/day} \approx 12,000\text{ QPS}$
   - Writes: $100\text{M} \times 1 = 100\text{M writes/day} \approx 1,200\text{ QPS}$
   - Peak QPS: Multiply average by $2\times - 5\times$ (e.g., $24,000\text{ QPS}$)
2. **Storage Estimation (5-year horizon):**
   - Size per write: e.g., $500\text{ bytes}$
   - Daily storage: $100\text{M} \times 500\text{ B} = 50\text{ GB/day}$
   - 5-Year Storage: $50\text{ GB} \times 365 \times 5 \approx 91.25\text{ TB}$
3. **Memory / Cache Estimation (80/20 Rule):**
   - $20\%$ of daily read volume generates $80\%$ of traffic.
   - Cache size = $20\% \times (\text{Daily Read Data})$.
4. **Network Bandwidth:**
   - Ingress = Write QPS $\times$ Average write payload
   - Egress = Read QPS $\times$ Average read payload

---

## 📌 Phase 3: High-Level Architecture (10 – 25 min)

1. **Define API Contracts:**
   - Show 2-3 essential endpoints with HTTP verb, path, query params, and JSON payloads.
2. **Define Data Models:**
   - Tables/entities, primary key, partition key, and key foreign references.
   - Justify SQL (ACID, complex relational queries) vs NoSQL (horizontal partitioning, flexible schema, high write throughput).
3. **Draw the End-to-End Block Diagram:**
   - Client (Web/Mobile) $\rightarrow$ DNS / CDN $\rightarrow$ Load Balancer $\rightarrow$ API Gateway $\rightarrow$ Stateless App Servers $\rightarrow$ Database / Cache / Message Queue.
4. **Trace the Golden Path:**
   - Walk through the write path step-by-step.
   - Walk through the read path step-by-step.

---

## 📌 Phase 4: Component Deep-Dives (25 – 38 min)

This is where you showcase senior technical depth. Pick the 2-3 most technically challenging aspects of the specific system:

- **For URL Shortener:** How is the short key generated (Base62 vs MD5 vs KGS)? How to prevent collision?
- **For Rate Limiter:** Token Bucket vs Sliding Window in Redis using Lua scripts to prevent race conditions.
- **For News Feed:** Fan-out on write (push) vs Fan-out on read (pull) vs Hybrid approach for celebrities.
- **For Chat:** WebSocket connection management, heartbeat pings, message ordering via sequence numbers.
- **For Flash Sale:** Concurrency control, distributed locks (Redlock), inventory reservation with Redis + DB reconciliation.

---

## 📌 Phase 5: Bottlenecks, Fault Tolerance & Wrap-Up (38 – 45 min)

1. **Single Points of Failure (SPOF):**
   - Every tier must have at least $N+1$ redundancy across Availability Zones (AZs).
2. **Database Scaling & Replication:**
   - Primary-Replica replication, Read Replicas, Sharding key strategy, handling replication lag.
3. **Cache Invalidation & Edge Failures:**
   - Cache stampede (thundering herd), cache penetration (Bloom filters), cache avalanche (jittered TTLs).
4. **Observability & Health Monitoring:**
   - Metrics (P95/P99 latency, error rates 5xx), Distributed Tracing (Jaeger/Zipkin), Structured Logging (ELK/OpenSearch).
5. **Future Improvements:**
   - Mention 1-2 practical enhancements you would make given more time.
