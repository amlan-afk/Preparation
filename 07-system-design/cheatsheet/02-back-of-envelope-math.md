# 🔢 Back-of-the-Envelope Estimation Cheat Sheet

Quick mental math formulas and latency numbers every backend engineer must know by heart.

---

## ⚡ Latency Numbers Every Engineer Should Know

| Operation | Time (ns / µs / ms) | Human Equivalent Scale |
|---|:---:|:---:|
| L1 cache reference | $0.5\text{ ns}$ | 1 heartbeat |
| Branch mispredict | $5\text{ ns}$ | 10 heartbeats |
| L2 cache reference | $7\text{ ns}$ | 14 heartbeats |
| Mutex lock/unlock | $25\text{ ns}$ | 50 heartbeats |
| Main memory (RAM) reference | $100\text{ ns}$ | 3.3 minutes |
| Compress 1KB with Zstandard | $2.0\text{ µs}$ | ~1 hour |
| Send 2KB over 1 Gbps network | $20\text{ µs}$ | ~7 hours |
| Read 1 MB sequentially from memory | $250\text{ µs}$ | ~3.5 days |
| Round trip within same datacenter | $500\text{ µs}$ | ~1 week |
| Read 1 MB sequentially from SSD | $1,000\text{ µs} = 1\text{ ms}$ | ~2 weeks |
| Disk seek (Rotational HDD) | $10,000\text{ µs} = 10\text{ ms}$ | ~20 weeks |
| Read 1 MB sequentially from HDD | $20,000\text{ µs} = 20\text{ ms}$ | ~40 weeks |
| Send packet CA to Netherlands and back | $150,000\text{ µs} = 150\text{ ms}$ | ~6 years |

### Core Takeaways:
1. **Memory is $\approx 10,000\times$ faster than Disk seek.** (Why in-memory caches like Redis transform throughput).
2. **Network round trips across datacenters dominate user-perceived latency.** (Why edge CDNs and regional point-of-presence matter).
3. **Sequential I/O is $10-50\times$ faster than Random I/O on disk.** (Why LSM-Trees in Cassandra/RocksDB and append-only logs in Kafka outperform random B-Tree disk writes).

---

## 📐 Powers of Two & Storage Conversions

| Power of 2 | Exact Value | Approx (Decimal) | Prefix |
|:---:|:---:|:---:|:---:|
| $2^{10}$ | 1,024 | $1\text{ Thousand} (10^3)$ | $1\text{ KB}$ |
| $2^{20}$ | 1,048,576 | $1\text{ Million} (10^6)$ | $1\text{ MB}$ |
| $2^{30}$ | 1,073,741,824 | $1\text{ Billion} (10^9)$ | $1\text{ GB}$ |
| $2^{40}$ | 1,099,511,627,776 | $1\text{ Trillion} (10^{12})$ | $1\text{ TB}$ |
| $2^{50}$ | 1,125,899,906,842,624 | $1\text{ Quadrillion} (10^{15})$ | $1\text{ PB}$ |

---

## ⏱️ Seconds Conversion (The 86,400 Rule)

- 1 day has $24 \times 60 \times 60 = 86,400\text{ seconds} \approx 100,000\text{ seconds} = 10^5\text{ seconds}$.
- **Quick QPS Shortcut:**
  $$\text{Daily Requests} / 10^5 \approx \text{Average QPS}$$
  - $1\text{ Million requests/day} \approx 10\text{ QPS}$
  - $10\text{ Million requests/day} \approx 100\text{ QPS}$
  - $100\text{ Million requests/day} \approx 1,000\text{ QPS}$
  - $1\text{ Billion requests/day} \approx 10,000\text{ QPS}$
- **Peak QPS:** Assume $2\times$ to $5\times$ average QPS.

---

## 💾 Standard Data Sizes
- Char: $1\text{ byte}$ (ASCII) or $2-4\text{ bytes}$ (UTF-8)
- Integer: $4\text{ bytes}$ (32-bit), $8\text{ bytes}$ (64-bit / Long)
- UUID / Timestamp: $16\text{ bytes}$ (128-bit) / $8\text{ bytes}$ (64-bit epoch)
- Average JSON metadata payload: $500\text{ B} - 2\text{ KB}$
- Short text message / Tweet: $140-280\text{ chars} \approx 300-500\text{ bytes}$
- Compressed thumbnail image: $20-50\text{ KB}$
- High-res photo: $2-5\text{ MB}$
- 1 minute of 1080p video: $\approx 20-50\text{ MB}$

---

## 🎯 The 80/20 Caching Rule (Pareto Principle)
- $20\%$ of active data accounts for $80\%$ of all read traffic.
- **Cache Memory Formula:**
  $$\text{Cache RAM Required} = 0.20 \times (\text{Total Daily Read Data})$$
