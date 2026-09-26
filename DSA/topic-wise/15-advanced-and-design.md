# 🏗️ Advanced & System / Class Design — Core Mastery Guide

> **Total Questions:** 15 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 3 &nbsp;|&nbsp; 🟡 Medium: 9 &nbsp;|&nbsp; 🔴 Hard: 3  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Object-oriented data structure design: LRU/LFU Caches, O(1) Randomized collections, Time-based key-value stores, and Circular buffers.

### Mental Triggers & "When to Think of Advanced & System / Class Design"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Advanced & System / Class Design

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 236 | [146](https://leetcode.com/problems/lru-cache) | [LRU Cache](https://leetcode.com/problems/lru-cache) | 🟡 Medium | Doubly Linked List + Hash Map (O(1) get and put) | `O(1) ops` | `O(Capacity)` | Amazon, Google, Microsoft, Facebook (+51 more) |
| 237 | [460](https://leetcode.com/problems/lfu-cache) | [LFU Cache](https://leetcode.com/problems/lfu-cache) | 🔴 Hard | Frequency doubly linked lists + min-frequency tracker | `O(1) ops` | `O(Capacity)` | Amazon, Google, Bloomberg, Microsoft (+9 more) |
| 238 | [380](https://leetcode.com/problems/insert-delete-getrandom-o1) | [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1) | 🟡 Medium | Dynamic array + Hash map index lookup with swap-to-back deletion | `O(1) ops` | `O(N)` | Amazon, Linkedin, Google, Facebook (+24 more) |
| 239 | [381](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed) | [Insert Delete GetRandom O(1) - Duplicates allowed](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed) | 🔴 Hard | Dynamic array + Hash map of index sets | `O(1) avg` | `O(N)` | Facebook, Linkedin, Google, Uber (+6 more) |
| 240 | [355](https://leetcode.com/problems/design-twitter) | [Design Twitter](https://leetcode.com/problems/design-twitter) | 🟡 Medium | OO Design with user follow graph and k-way heap feed merge | `O(K log F)` | `O(U + T)` | Twitter, Yelp, Doordash, Amazon (+1 more) |
| 241 | [271](https://leetcode.com/problems/encode-and-decode-strings) | [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings) | 🟡 Medium | Length-prefixed string encoding (e.g., '4#love5#coder') | `O(N)` | `O(1) aux` | Google, Bloomberg, Square, Microsoft (+1 more) |
| 242 | [981](https://leetcode.com/problems/time-based-key-value-store) | [Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store) | 🟡 Medium | Hash Map of lists with binary search on timestamps | `O(1) set, O(log N) get` | `O(N)` | Google, Lyft, Amazon, Sumologic (+14 more) |
| 243 | [1396](https://leetcode.com/problems/design-underground-system) | [Design Underground System](https://leetcode.com/problems/design-underground-system) | 🟡 Medium | Two hash maps: check-in trips and travel route statistics | `O(1) ops` | `O(Stations + Customers)` | Bloomberg |
| 244 | [341](https://leetcode.com/problems/flatten-nested-list-iterator) | [Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator) | 🟡 Medium | Stack of iterators or recursive generator | `O(1) amortized` | `O(D)` | Linkedin, Facebook, Amazon, Uber (+14 more) |
| 245 | [622](https://leetcode.com/problems/design-circular-queue) | [Design Circular Queue](https://leetcode.com/problems/design-circular-queue) | 🟡 Medium | Fixed-size circular array with head and count pointers | `O(1) ops` | `O(K)` | Facebook, Amazon, Microsoft, Google (+6 more) |
| 246 | [641](https://leetcode.com/problems/design-circular-deque) | [Design Circular Deque](https://leetcode.com/problems/design-circular-deque) | 🟡 Medium | Fixed-size circular array with front and rear indices | `O(1) ops` | `O(K)` | Facebook |
| 247 | [706](https://leetcode.com/problems/design-hashmap) | [Design HashMap](https://leetcode.com/problems/design-hashmap) | 🟢 Easy | Array of buckets with linked lists / chaining | `O(1) avg` | `O(N)` | Amazon, Microsoft, Goldman Sachs, Linkedin (+15 more) |
| 248 | [705](https://leetcode.com/problems/design-hashset) | [Design HashSet](https://leetcode.com/problems/design-hashset) | 🟢 Easy | Direct address table or hash buckets with chaining | `O(1) avg` | `O(N)` | Google, Amazon |
| 249 | [432](https://leetcode.com/problems/all-oone-data-structure) | [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure) | 🔴 Hard | Doubly linked bucket list sorted by frequency + Hash map of node pointers | `O(1) ops` | `O(N)` | Linkedin, Facebook, Google, Amazon (+3 more) |
| 250 | [359](https://leetcode.com/problems/logger-rate-limiter) | [Logger Rate Limiter](https://leetcode.com/problems/logger-rate-limiter) | 🟢 Easy | Hash Map or sliding window queue of timestamps | `O(1)` | `O(Unique Messages)` | Google, Uber, Amazon, Apple (+4 more) |

---

## 🛠️ Step-by-Step Problem Solving Framework

1. **Clarify Inputs & Edge Cases:**
   - Empty input, single element, duplicates, negative numbers, extreme values ($10^9$ overflow).
2. **State Brute Force & Why It Fails:**
   - State the naive $O(N^2)$ or $O(2^N)$ approach to prove baseline understanding.
3. **Formulate Invariant / Core Pattern:**
   - State the invariant: *"At each step $i$, our data structure maintains the optimal window / balance / prefix sum."*
4. **Dry Run with Concrete Example:**
   - Trace through a small, non-trivial test case (3-4 elements) to catch off-by-one errors.
5. **Code with Modular Cleanliness:**
   - Clean variable naming (`left`, `right`, `curr_sum`, `max_len`), handle edge boundaries cleanly.
6. **Complexity Defense:**
   - State tight Time and Space bounds explicitly before the interviewer asks.

---

## 🚀 Navigation

- [⬅️ Back to Topic Directory Index](./README.md)
- [📊 View Master Progress Tracker (CSV)](../250-master-tracker.csv)
- [🏢 Company-Wise Question Bank](../company-wise/README.md)
