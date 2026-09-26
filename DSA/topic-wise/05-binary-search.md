# 🔍 Binary Search — Core Mastery Guide

> **Total Questions:** 15 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 3 &nbsp;|&nbsp; 🟡 Medium: 9 &nbsp;|&nbsp; 🔴 Hard: 3  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Logarithmic time search algorithms, rotated sorted arrays, matrix searches, and binary search on monotonic answer spaces.

### Mental Triggers & "When to Think of Binary Search"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Binary Search

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 68 | [704](https://leetcode.com/problems/binary-search) | [Binary Search](https://leetcode.com/problems/binary-search) | 🟢 Easy | Classic two-pointer middle convergence | `O(log N)` | `O(1)` | Microsoft, Amazon |
| 69 | [74](https://leetcode.com/problems/search-a-2d-matrix) | [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix) | 🟡 Medium | Virtual 1D array binary search via row/col mapping | `O(log(M*N))` | `O(1)` | Amazon, Microsoft, Facebook, Adobe (+4 more) |
| 70 | [240](https://leetcode.com/problems/search-a-2d-matrix-ii) | [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii) | 🟡 Medium | Search from top-right or bottom-left corner | `O(M + N)` | `O(1)` | Amazon, Microsoft, Google, Facebook (+16 more) |
| 71 | [33](https://leetcode.com/problems/search-in-rotated-sorted-array) | [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array) | 🟡 Medium | Identify sorted half and check if target lies within | `O(log N)` | `O(1)` | Amazon, Facebook, Microsoft, Google (+32 more) |
| 72 | [153](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array) | [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array) | 🟡 Medium | Compare mid with right boundary to locate inflection | `O(log N)` | `O(1)` | Amazon, Microsoft, Google, Goldman Sachs (+9 more) |
| 73 | [154](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii) | [Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii) | 🔴 Hard | Inflection search handling duplicate boundaries | `O(log N) avg, O(N) worst` | `O(1)` | Google, Facebook, Adobe, Amazon (+1 more) |
| 74 | [875](https://leetcode.com/problems/koko-eating-bananas) | [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas) | 🟡 Medium | Binary search on answer space [1, max(piles)] | `O(N log(max(P)))` | `O(1)` | Airbnb, Facebook, Google, Adobe (+1 more) |
| 75 | [1011](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days) | [Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days) | 🟡 Medium | Binary search on capacity range [max(w), sum(w)] | `O(N log(sum(W)))` | `O(1)` | Google, Amazon, Apple, Uber |
| 76 | [410](https://leetcode.com/problems/split-array-largest-sum) | [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum) | 🔴 Hard | Binary search on maximum subarray sum answer | `O(N log(sum(nums)))` | `O(1)` | Google, Amazon, Facebook, Baidu |
| 77 | [4](https://leetcode.com/problems/median-of-two-sorted-arrays) | [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays) | 🔴 Hard | Binary search on smaller array partition boundary | `O(log(min(M, N)))` | `O(1)` | Amazon, Google, Microsoft, Goldman Sachs (+27 more) |
| 78 | [34](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array) | [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array) | 🟡 Medium | Two binary searches for lower and upper bounds | `O(log N)` | `O(1)` | Facebook, Google, Linkedin, Uber (+13 more) |
| 79 | [35](https://leetcode.com/problems/search-insert-position) | [Search Insert Position](https://leetcode.com/problems/search-insert-position) | 🟢 Easy | Lower bound binary search | `O(log N)` | `O(1)` | Google, Amazon, Adobe, Apple (+3 more) |
| 80 | [162](https://leetcode.com/problems/find-peak-element) | [Find Peak Element](https://leetcode.com/problems/find-peak-element) | 🟡 Medium | Slope checking: climb uphill towards larger neighbor | `O(log N)` | `O(1)` | Facebook, Google, Amazon, Bloomberg (+9 more) |
| 81 | [69](https://leetcode.com/problems/sqrtx) | [Sqrt(x)](https://leetcode.com/problems/sqrtx) | 🟢 Easy | Binary search on integer square root range | `O(log X)` | `O(1)` | Google, Linkedin, Bloomberg, Amazon (+8 more) |
| 82 | [540](https://leetcode.com/problems/single-element-in-a-sorted-array) | [Single Element in a Sorted Array](https://leetcode.com/problems/single-element-in-a-sorted-array) | 🟡 Medium | Binary search on even/odd index pairing invariant | `O(log N)` | `O(1)` | Amazon, Google, Facebook, Microsoft (+4 more) |

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
