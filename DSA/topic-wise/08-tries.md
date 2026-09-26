# 🌿 Tries (Prefix Trees) — Core Mastery Guide

> **Total Questions:** 7 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 0 &nbsp;|&nbsp; 🟡 Medium: 5 &nbsp;|&nbsp; 🔴 Hard: 2  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Prefix-based tree retrieval, auto-complete dictionaries, wildcard pattern lookups, and Bitwise Tries for maximum XOR calculations.

### Mental Triggers & "When to Think of Tries (Prefix Trees)"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Tries (Prefix Trees)

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 123 | [208](https://leetcode.com/problems/implement-trie-prefix-tree) | [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree) | 🟡 Medium | TrieNode with 26-child array and isEnd flag | `O(L) ops` | `O(Total Chars)` | Amazon, Google, Microsoft, Facebook (+11 more) |
| 124 | [211](https://leetcode.com/problems/add-and-search-word-data-structure-design) | [Design Add and Search Words Data Structure](https://leetcode.com/problems/add-and-search-word-data-structure-design) | 🟡 Medium | Trie with DFS branching for '.' wildcards | `O(26^M) wildcard` | `O(Total Chars)` | Facebook, Amazon, Google, Uber (+5 more) |
| 125 | [212](https://leetcode.com/problems/word-search-ii) | [Word Search II](https://leetcode.com/problems/word-search-ii) | 🔴 Hard | Backtracking grid search guided by Trie with pruning | `O(M * 4 * 3^(L-1))` | `O(Total Chars)` | Amazon, Microsoft, Google, Facebook (+14 more) |
| 126 | [648](https://leetcode.com/problems/replace-words) | [Replace Words](https://leetcode.com/problems/replace-words) | 🟡 Medium | Trie prefix lookup for shortest dictionary root | `O(N * L)` | `O(Total Chars)` | Uber |
| 127 | [677](https://leetcode.com/problems/map-sum-pairs) | [Map Sum Pairs](https://leetcode.com/problems/map-sum-pairs) | 🟡 Medium | Trie storing prefix values or delta score updates | `O(L) ops` | `O(Total Chars)` | Akuna Capital |
| 128 | [421](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array) | [Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array) | 🟡 Medium | Bitwise Trie (0/1 branching) querying opposite bits | `O(32 * N)` | `O(32 * N)` | Google, Amazon |
| 129 | [745](https://leetcode.com/problems/prefix-and-suffix-search) | [Prefix and Suffix Search](https://leetcode.com/problems/prefix-and-suffix-search) | 🔴 Hard | Trie storing suffix#prefix combined keys | `O(L^2) insert` | `O(N * L^2)` | Google, Facebook |

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
