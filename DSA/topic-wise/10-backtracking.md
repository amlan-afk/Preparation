# 🔄 Backtracking — Core Mastery Guide

> **Total Questions:** 15 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 0 &nbsp;|&nbsp; 🟡 Medium: 13 &nbsp;|&nbsp; 🔴 Hard: 2  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Exhaustive combinatorial state-space searches, subsets, permutations, combination sums, constraint satisfaction, and pruning.

### Mental Triggers & "When to Think of Backtracking"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Backtracking

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 143 | [78](https://leetcode.com/problems/subsets) | [Subsets](https://leetcode.com/problems/subsets) | 🟡 Medium | Cascading recursion: choose / don't choose element | `O(N * 2^N)` | `O(N)` | Facebook, Amazon, Microsoft, Google (+11 more) |
| 144 | [90](https://leetcode.com/problems/subsets-ii) | [Subsets II](https://leetcode.com/problems/subsets-ii) | 🟡 Medium | Sort first, skip duplicate adjacent elements during loop | `O(N * 2^N)` | `O(N)` | Facebook, Amazon, Bloomberg, Microsoft |
| 145 | [39](https://leetcode.com/problems/combination-sum) | [Combination Sum](https://leetcode.com/problems/combination-sum) | 🟡 Medium | Unbounded selection DFS with target deduction | `O(2^(T/min))` | `O(T/min)` | Amazon, Airbnb, Facebook, Microsoft (+14 more) |
| 146 | [40](https://leetcode.com/problems/combination-sum-ii) | [Combination Sum II](https://leetcode.com/problems/combination-sum-ii) | 🟡 Medium | Single-use elements with duplicate skipping on same level | `O(2^N)` | `O(N)` | Amazon, Facebook, Microsoft, Linkedin (+8 more) |
| 147 | [216](https://leetcode.com/problems/combination-sum-iii) | [Combination Sum III](https://leetcode.com/problems/combination-sum-iii) | 🟡 Medium | Fixed-length k backtracking on digits 1..9 | `O(9! / (9-k)!)` | `O(K)` | Google, Microsoft, Amazon, Bloomberg |
| 148 | [46](https://leetcode.com/problems/permutations) | [Permutations](https://leetcode.com/problems/permutations) | 🟡 Medium | Visited boolean array or in-place element swapping | `O(N * N!)` | `O(N)` | Facebook, Microsoft, Amazon, Google (+19 more) |
| 149 | [47](https://leetcode.com/problems/permutations-ii) | [Permutations II](https://leetcode.com/problems/permutations-ii) | 🟡 Medium | Sort array, skip duplicate candidates when unvisited | `O(N * N!)` | `O(N)` | Linkedin, Facebook, Amazon, Vmware (+5 more) |
| 150 | [77](https://leetcode.com/problems/combinations) | [Combinations](https://leetcode.com/problems/combinations) | 🟡 Medium | Backtracking choosing k numbers from 1..n | `O(k * C(n, k))` | `O(k)` | Microsoft, Google, Facebook, Amazon (+3 more) |
| 151 | [79](https://leetcode.com/problems/word-search) | [Word Search](https://leetcode.com/problems/word-search) | 🟡 Medium | 4-directional grid DFS with in-place cell marking | `O(M * N * 3^L)` | `O(L)` | Amazon, Facebook, Microsoft, Google (+19 more) |
| 152 | [131](https://leetcode.com/problems/palindrome-partitioning) | [Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning) | 🟡 Medium | Backtracking prefix palindrome validation with memo | `O(N * 2^N)` | `O(N)` | Amazon, Adobe, Google, Uber (+4 more) |
| 153 | [17](https://leetcode.com/problems/letter-combinations-of-a-phone-number) | [Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number) | 🟡 Medium | Digit keypad recursion building combinations | `O(4^N)` | `O(N)` | Facebook, Amazon, Microsoft, Google (+21 more) |
| 154 | [51](https://leetcode.com/problems/n-queens) | [N-Queens](https://leetcode.com/problems/n-queens) | 🔴 Hard | Column and diagonal bitsets/sets tracking conflicts | `O(N!)` | `O(N)` | Facebook, Microsoft, Amazon, Google (+8 more) |
| 155 | [37](https://leetcode.com/problems/sudoku-solver) | [Sudoku Solver](https://leetcode.com/problems/sudoku-solver) | 🔴 Hard | Row, col, box bitmasks with backtracking cell filling | `O(9^empty)` | `O(1)` | Google, Microsoft, Amazon, Facebook (+12 more) |
| 156 | [22](https://leetcode.com/problems/generate-parentheses) | [Generate Parentheses](https://leetcode.com/problems/generate-parentheses) | 🟡 Medium | Backtrack adding '(' if open < n and ')' if close < open | `O(4^N / sqrt(N))` | `O(N)` | Amazon, Microsoft, Google, Facebook (+20 more) |
| 157 | [93](https://leetcode.com/problems/restore-ip-addresses) | [Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses) | 🟡 Medium | 3-dot partitioning with [0, 255] segment validation | `O(1)` | `O(1)` | Amazon, Microsoft, Facebook, Vmware (+3 more) |

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
