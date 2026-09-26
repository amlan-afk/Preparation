# System Design: Design a Distributed Unique ID Generator (Twitter Snowflake)

> **Interview Level:** SDE 2 / Senior Backend  
> **Frequency:** ★★★★★ (Core Distributed Building Block asked at Twitter, Meta, Stripe, Amazon)  
> **Core Concepts:** 64-bit Bitmasking, Epoch Timestamps, Datacenter / Worker IDs, Sequence Rollover, Clock Drift / NTP Skew.

---

## 1. Requirements & Scope

### Functional Requirements
1. **Uniqueness:** Generated IDs must be globally unique across all datacenters and nodes.
2. **Sortable by Time:** IDs must be roughly time-ordered (so databases can use them as B-Tree cluster indexes without fragmentation).
3. **64-bit Integer:** Must fit within a signed 64-bit integer (`BIGINT` in SQL / `long` in Java) for performance.

### Non-Functional Requirements
1. **Extreme Throughput:** Support generating $> 100,000\text{ IDs/second}$ per server without network coordination.
2. **High Availability:** ID generation must never be blocked by network partitions.

---

## 2. Deep Dive: Twitter Snowflake 64-Bit Structure

```
+-------------------------------------------------------------------------+
| 1 bit  | 41 bits (Timestamp) | 5 bits (DC) | 5 bits (Worker) | 12 bits  |
| Unused | Milliseconds from   | Datacenter  | Machine/Worker  | Sequence |
| (0)    | Custom Epoch        | ID (0-31)   | ID (0-31)       | (0-4095) |
+-------------------------------------------------------------------------+
```

### Breakdown of the 64 Bits:
1. **1 Unused Sign Bit:** Always `0` (ensures ID is positive integer).
2. **41 Timestamp Bits:**
   - Milliseconds since custom epoch (e.g. `Nov 04, 2010 01:42:54 UTC`).
   - Range: $2^{41} - 1 = 2,199,023,255,551\text{ ms} \approx 69.7\text{ years}$.
3. **5 Datacenter ID Bits:**
   - Supports up to $2^5 = 32\text{ datacenters}$.
4. **5 Worker / Machine ID Bits:**
   - Supports up to $2^5 = 32\text{ servers per datacenter}$ ($32 \times 32 = 1,024\text{ total generator nodes}$).
5. **12 Sequence Number Bits:**
   - Monotonically incremented counter for IDs generated within the exact same millisecond on the same server.
   - Range: $2^{12} = 4,096\text{ IDs per millisecond per server}$ ($4,096,000\text{ IDs/sec/node}$).

---

## 3. Clock Drift & Edge Case Handling

1. **NTP Clock Backward Drift:**
   - If the system clock is set back by NTP sync, a server could generate duplicate IDs.
   - *Fix:* The generator caches `last_timestamp`. If `current_timestamp < last_timestamp`, the generator throws an error, waits for the clock to catch up, or rejects requests until time moves forward.
2. **Sequence Number Overflow in Same Millisecond:**
   - If a single server generates $> 4,096$ IDs in a single millisecond, the sequence counter rolls over.
   - *Fix:* The worker spins in a tight loop waiting for the next millisecond before issuing the next ID.
