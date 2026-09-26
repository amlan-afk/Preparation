# System design practice

## Repeatable interview flow

1. Clarify users, use cases, scope, and success criteria.
2. Estimate scale and identify latency, availability, privacy, and consistency needs.
3. Define API contracts and core data model.
4. Draw a simple end-to-end architecture.
5. Deep dive into storage, indexing, caching, queues, and key algorithms.
6. Walk through bottlenecks, dependency failure, retries, and overload behavior.
7. Cover security, observability, rollout, and operational cost.
8. Summarize trade-offs and likely next improvements.

## Fundamentals checklist

- [ ] Horizontal vs vertical scaling and load balancing
- [ ] Cache placement, TTL, invalidation, cache stampede
- [ ] CDN and static asset delivery
- [ ] SQL vs NoSQL, indexes, replication, sharding
- [ ] CAP and practical consistency choices
- [ ] Queues, Kafka, async processing
- [ ] Redis use cases
- [ ] Rate limiting
- [ ] Availability, latency, fault isolation

## Practice sequence & detailed solutions

### Fundamentals & web services

- [URL shortener (TinyURL / Bitly)](./solutions/01-url-shortener.md) — Base62, KGS, 301 vs 302, sharding.
- [Distributed rate limiter](./solutions/02-distributed-rate-limiter.md) — Token/Leaky bucket, Redis Lua sliding window.
- [Scalable notification system](./solutions/03-notification-system.md) — Multi-channel, priority queues, idempotency, DLQ.
- [Distributed unique ID generator (Snowflake)](./solutions/11-unique-id-generator-snowflake.md) — 64-bit epoch, NTP clock drift.
- [Distributed in-memory cache (Redis)](./solutions/09-distributed-cache.md) — Consistent hashing, virtual nodes, LRU eviction.

### High-concurrency & real-time systems

- [Ticketmaster / flash sale booking](./solutions/04-ticketmaster-flash-sale.md) — Distributed locks, concurrency control, virtual waiting room.
- [Social media feed (Twitter / Instagram)](./solutions/05-news-feed-twitter.md) — Push vs pull vs hybrid fan-out, cursor pagination.
- [Real-time chat (WhatsApp / Slack)](./solutions/06-whatsapp-chat-system.md) — WebSockets, session registry, sequence ordering.
- [Ride-sharing dispatch (Uber / Lyft)](./solutions/07-uber-ride-sharing.md) — Uber H3 hex grid vs Geohash, 250k QPS location stream.
- [Video streaming platform (YouTube / Netflix)](./solutions/08-youtube-netflix-streaming.md) — DAG transcoding, Adaptive Bitrate HLS/DASH, CDN.

### Enterprise scale & search systems

- [Search autocomplete / typeahead](./solutions/10-autocomplete-typeahead.md) — Trie with precomputed top-k, Kafka log aggregator.
- [Cloud storage & file sync (Dropbox / Drive)](./solutions/12-dropbox-google-drive.md) — 4MB chunking, Content-Addressable Storage, delta sync.
- [Distributed metrics & logging (Datadog)](./solutions/13-metrics-monitoring-system.md) — TSDB, Gorilla compression, rollup compaction.
- [Payment gateway & financial ledger (Stripe)](./solutions/14-payment-system-stripe.md) — Double-entry bookkeeping, idempotency keys, Saga.
- [Healthcare provider search & pricing (UMR)](./solutions/15-healthcare-provider-search.md) — OpenSearch, GraphQL federation, Resilience4j fallback.

---

### Your project: provider search & pricing

```text
React client → Spring Boot API → GraphQL service → provider system
                         ↘ optional cache (only if appropriate)
```

Detailed design and interview defense: [07-system-design/solutions/15-healthcare-provider-search.md](./solutions/15-healthcare-provider-search.md)

---

## 🎯 Mock Rehearsal & Readiness Tracker

| # | System Design Topic | Solution Link | Rehearsal Date | Confidence (1–5) | Key Follow-Up Notes / Missing Detail | Next Review |
|---|---|---|---|:---:|---|---|
| 01 | URL Shortener | [01-url-shortener.md](./solutions/01-url-shortener.md) | | | | |
| 02 | Distributed Rate Limiter | [02-distributed-rate-limiter.md](./solutions/02-distributed-rate-limiter.md) | | | | |
| 03 | Notification System | [03-notification-system.md](./solutions/03-notification-system.md) | | | | |
| 04 | Ticketmaster Flash Sale | [04-ticketmaster-flash-sale.md](./solutions/04-ticketmaster-flash-sale.md) | | | | |
| 05 | Social News Feed | [05-news-feed-twitter.md](./solutions/05-news-feed-twitter.md) | | | | |
| 06 | WhatsApp Real-Time Chat | [06-whatsapp-chat-system.md](./solutions/06-whatsapp-chat-system.md) | | | | |
| 07 | Uber Ride-Sharing | [07-uber-ride-sharing.md](./solutions/07-uber-ride-sharing.md) | | | | |
| 08 | YouTube Video Streaming | [08-youtube-netflix-streaming.md](./solutions/08-youtube-netflix-streaming.md) | | | | |
| 09 | Distributed Cache | [09-distributed-cache.md](./solutions/09-distributed-cache.md) | | | | |
| 10 | Autocomplete Typeahead | [10-autocomplete-typeahead.md](./solutions/10-autocomplete-typeahead.md) | | | | |
| 11 | Unique ID Generator | [11-unique-id-generator-snowflake.md](./solutions/11-unique-id-generator-snowflake.md) | | | | |
| 12 | Dropbox Cloud Sync | [12-dropbox-google-drive.md](./solutions/12-dropbox-google-drive.md) | | | | |
| 13 | Datadog Metrics System | [13-metrics-monitoring-system.md](./solutions/13-metrics-monitoring-system.md) | | | | |
| 14 | Stripe Payment Gateway | [14-payment-system-stripe.md](./solutions/14-payment-system-stripe.md) | | | | |
| 15 | Healthcare Provider Search | [15-healthcare-provider-search.md](./solutions/15-healthcare-provider-search.md) | | | | |
