# ⛰️ Heaps & Priority Queues — Core Mastery Guide

> **Total Questions:** 13 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 2 &nbsp;|&nbsp; 🟡 Medium: 8 &nbsp;|&nbsp; 🔴 Hard: 3  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Dynamic min/max tracking, k-way merges, top-k frequent elements, median stream management, and task scheduling.

### Mental Triggers & "When to Think of Heaps & Priority Queues"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Heaps & Priority Queues

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 130 | [703](https://leetcode.com/problems/kth-largest-element-in-a-stream) | [Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream) | 🟢 Easy | Min-heap of fixed capacity k | `O(log K) per add` | `O(K)` | Amazon, Facebook, Google, Microsoft (+5 more) |
| 131 | [215](https://leetcode.com/problems/kth-largest-element-in-an-array) | [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array) | 🟡 Medium | QuickSelect partition or Min-heap of size k | `O(N) avg, O(N log K) heap` | `O(K)` | Facebook, Amazon, Google, Microsoft (+23 more) |
| 132 | [973](https://leetcode.com/problems/k-closest-points-to-origin) | [K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin) | 🟡 Medium | Max-heap of size k or QuickSelect on distances | `O(N log K)` | `O(K)` | Amazon, Facebook, Google, Apple (+11 more) |
| 133 | [621](https://leetcode.com/problems/task-scheduler) | [Task Scheduler](https://leetcode.com/problems/task-scheduler) | 🟡 Medium | Greedy idle calculation by most frequent task | `O(N)` | `O(26)` | Facebook, Google, Uber, Microsoft (+7 more) |
| 134 | [1383](https://leetcode.com/problems/maximum-performance-of-a-team) | [Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team) | 🔴 Hard | Sort by efficiency descending, maintain min-heap of speeds | `O(N log N)` | `O(K)` | Citrix |
| 135 | [632](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists) | [Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists) | 🔴 Hard | Min-heap tracking current elements across K lists + max tracker | `O(N log K)` | `O(K)` | Facebook, Google, Amazon, Pinterest (+4 more) |
| 136 | [378](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix) | [Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix) | 🟡 Medium | Min-heap of row pointers or binary search on matrix values | `O(K log N) or O(N log(max-min))` | `O(N)` | Amazon, Facebook, Google, Microsoft (+6 more) |
| 137 | [373](https://leetcode.com/problems/find-k-pairs-with-smallest-sums) | [Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums) | 🟡 Medium | Min-heap expanding pairs (i, j+1) | `O(K log K)` | `O(K)` | Amazon, Linkedin, Google, Facebook (+3 more) |
| 138 | [451](https://leetcode.com/problems/sort-characters-by-frequency) | [Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency) | 🟡 Medium | Max-heap of frequencies or bucket sort | `O(N log K)` | `O(N)` | Bloomberg, Amazon, Google, Uber (+3 more) |
| 139 | [692](https://leetcode.com/problems/top-k-frequent-words) | [Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words) | 🟡 Medium | Min-heap with custom frequency comparator / Trie | `O(N log K)` | `O(N)` | Amazon, Facebook, Google, Bloomberg (+13 more) |
| 140 | [767](https://leetcode.com/problems/reorganize-string) | [Reorganize String](https://leetcode.com/problems/reorganize-string) | 🟡 Medium | Max-heap of character frequencies, pair adjacent chars | `O(N log 26)` | `O(26)` | Facebook, Google, Amazon, Microsoft (+8 more) |
| 141 | [1046](https://leetcode.com/problems/last-stone-weight) | [Last Stone Weight](https://leetcode.com/problems/last-stone-weight) | 🟢 Easy | Max-heap smashing two heaviest stones | `O(N log N)` | `O(N)` | Amazon |
| 142 | [295](https://leetcode.com/problems/find-median-from-data-stream) | [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream) | 🔴 Hard | Two balancing heaps (max-heap lower, min-heap higher) | `O(log N) insert, O(1) find` | `O(N)` | Amazon, Google, Microsoft, Facebook (+19 more) |

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
