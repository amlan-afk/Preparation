# 🎯 Master System Design Solutions & Case Studies

A curated repository of **15 production-grade system design interview solutions**, covering the most frequently asked problems across Tier-1 tech companies (Google, Meta, Amazon, Apple, Netflix, Stripe, Uber).

Every design includes full requirements, back-of-the-envelope capacity estimations, API specifications, data models, high-level Mermaid architecture diagrams, component deep-dives, failure recovery strategies, and trade-off analyses.

---

## 🗺️ Master Solutions Directory

| # | System Design Prompt | Primary Archetype | Target Level | Guide Link | Key Technical Highlights |
|:---:|---|---|:---:|---|---|
| **01** | **URL Shortener** (TinyURL / Bitly) | Web Services & Key-Value | SDE 2 / Senior | [01-url-shortener.md](./01-url-shortener.md) | Base62 encoding, Key Generation Service (KGS), 301 vs 302 redirects, Sharding. |
| **02** | **Distributed API Rate Limiter** | Security & Traffic Shaping | SDE 2 / Senior | [02-distributed-rate-limiter.md](./02-distributed-rate-limiter.md) | Token/Leaky Bucket, Redis Lua atomic sliding-window script, fail-open resiliency. |
| **03** | **Notification Service** | Event-Driven & Messaging | SDE 2 / Senior | [03-notification-system.md](./03-notification-system.md) | Priority queues, APNs/FCM/Twilio integration, idempotency keys, Dead-Letter Queues. |
| **04** | **Ticketmaster / Flash Sale Booking** | High-Concurrency & E-Commerce | SDE 2 / Senior | [04-ticketmaster-flash-sale.md](./04-ticketmaster-flash-sale.md) | Distributed Redis locks, optimistic/pessimistic locking, virtual waiting room, double-booking prevention. |
| **05** | **Social Media Feed** (Twitter / Instagram) | Feeds & Social Networks | SDE 2 / Senior | [05-news-feed-twitter.md](./05-news-feed-twitter.md) | Push vs Pull vs Hybrid fan-out, celebrity hot-key handling, cursor pagination, Redis timeline ZSETs. |
| **06** | **Real-Time Chat** (WhatsApp / Slack) | Real-Time & WebSockets | SDE 2 / Senior | [06-whatsapp-chat-system.md](./06-whatsapp-chat-system.md) | WebSocket gateway, Redis session registry, Snowflake sequence ordering, presence heartbeat. |
| **07** | **Ride-Sharing Dispatch** (Uber / Lyft) | Geospatial & Real-Time | SDE 2 / Senior | [07-uber-ride-sharing.md](./07-uber-ride-sharing.md) | Uber H3 Hex grid vs Geohash, 250k QPS driver location ingestion, dynamic surge pricing, driver lock. |
| **08** | **Video Streaming** (YouTube / Netflix) | Media Pipelines & CDN | SDE 2 / Senior | [08-youtube-netflix-streaming.md](./08-youtube-netflix-streaming.md) | Video chunking, DAG transcoding workflows, Adaptive Bitrate Streaming (HLS/DASH), CDN caching. |
| **09** | **Distributed In-Memory Cache** (Redis) | Distributed Infrastructure | SDE 2 / Senior | [09-distributed-cache.md](./09-distributed-cache.md) | Consistent hashing ring with virtual nodes, $O(1)$ LRU eviction, replication, cache stampede prevention. |
| **10** | **Search Autocomplete / Typeahead** | Search & Information Retrieval | SDE 2 / Senior | [10-autocomplete-typeahead.md](./10-autocomplete-typeahead.md) | Trie with precomputed Top-K at nodes, offline Spark/Kafka frequency pipeline, sub-50ms latency. |
| **11** | **Unique ID Generator** (Snowflake) | Distributed Primitives | SDE 2 / Senior | [11-unique-id-generator-snowflake.md](./11-unique-id-generator-snowflake.md) | 64-bit bitmasking, custom epoch, datacenter/worker bits, sequence rollover, NTP clock drift. |
| **12** | **Cloud Storage & Sync** (Dropbox / Drive) | Storage Systems & File Sync | SDE 2 / Senior | [12-dropbox-google-drive.md](./12-dropbox-google-drive.md) | Content-Addressable Storage (CAS), 4MB chunking, Rabin fingerprint delta sync, deduplication. |
| **13** | **Distributed Metrics & Logging** (Datadog) | Observability & TSDB | SDE 2 / Senior | [13-metrics-monitoring-system.md](./13-metrics-monitoring-system.md) | Time-series database, Gorilla delta-of-delta compression, push vs pull ingestion, rollup worker. |
| **14** | **Payment Gateway & Ledger** (Stripe) | FinTech & Financial Ledgers | SDE 2 / Senior | [14-payment-system-stripe.md](./14-payment-system-stripe.md) | Double-entry bookkeeping, exactly-once processing, Saga orchestrator, daily bank reconciliation. |
| **15** | **Healthcare Provider Search & Pricing** | Domain-Specific (UMR / Health) | SDE 2 / Senior | [15-healthcare-provider-search.md](./15-healthcare-provider-search.md) | OpenSearch inverted index, GraphQL federation, Resilience4j circuit breakers, CMS transparency rules. |

---

## 🎯 How to Practice These Questions for SDE 2 Interviews

1. **Simulate Real Conditions (Whiteboard / CoderPad):**
   - Spend 5 minutes on scope and numbers.
   - Draw the architecture blocks before talking about databases.
   - Deep-dive into the 2 most unique components for that problem.
2. **Explain the "Why":**
   - Don't just say "we use Kafka"; say: *"We use Kafka here because we require multiple independent consumer groups to replay events with strict partition ordering."*
3. **Master the Cheatsheets:**
   - [⏱️ 45-Minute Interview Timing Protocol](../cheatsheet/01-framework-and-timing.md)
   - [🔢 Back-of-the-Envelope Math Cheat Sheet](../cheatsheet/02-back-of-envelope-math.md)
   - [🧱 Architectural Building Blocks](../cheatsheet/03-building-blocks.md)
