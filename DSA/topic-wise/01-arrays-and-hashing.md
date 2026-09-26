# 📦 Arrays & Hashing — Core Mastery Guide

> **Total Questions:** 23 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 5 &nbsp;|&nbsp; 🟡 Medium: 17 &nbsp;|&nbsp; 🔴 Hard: 1  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

The cornerstone of technical interviews. Focuses on frequency maps, prefix sums, cycle sorting, and constant-time lookups.

### Mental Triggers & "When to Think of Arrays & Hashing"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Arrays & Hashing

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 1 | [1](https://leetcode.com/problems/two-sum) | [Two Sum](https://leetcode.com/problems/two-sum) | 🟢 Easy | Hash Map lookup complement | `O(N)` | `O(N)` | Google, Amazon, Facebook, Microsoft (+68 more) |
| 2 | [217](https://leetcode.com/problems/contains-duplicate) | [Contains Duplicate](https://leetcode.com/problems/contains-duplicate) | 🟢 Easy | Hash Set frequency check | `O(N)` | `O(N)` | Amazon, Adobe, Microsoft, Yahoo (+6 more) |
| 3 | [242](https://leetcode.com/problems/valid-anagram) | [Valid Anagram](https://leetcode.com/problems/valid-anagram) | 🟢 Easy | Frequency array / Hash Map | `O(N)` | `O(1)` | Amazon, Google, Bloomberg, Microsoft (+15 more) |
| 4 | [49](https://leetcode.com/problems/group-anagrams) | [Group Anagrams](https://leetcode.com/problems/group-anagrams) | 🟡 Medium | Categorize by sorted string or tuple count | `O(N * K log K)` | `O(N * K)` | Amazon, Microsoft, Facebook, Google (+29 more) |
| 5 | [347](https://leetcode.com/problems/top-k-frequent-elements) | [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements) | 🟡 Medium | Bucket sort or Min-heap | `O(N)` | `O(N)` | Amazon, Facebook, Google, Bloomberg (+15 more) |
| 6 | [238](https://leetcode.com/problems/product-of-array-except-self) | [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self) | 🟡 Medium | Prefix and Suffix running products | `O(N)` | `O(1) aux` | Facebook, Amazon, Lyft, Goldman Sachs (+28 more) |
| 7 | [36](https://leetcode.com/problems/valid-sudoku) | [Valid Sudoku](https://leetcode.com/problems/valid-sudoku) | 🟡 Medium | Bitmask or Set tracking for row/col/box | `O(1)` | `O(1)` | Uber, Microsoft, Amazon, Google (+13 more) |
| 8 | [128](https://leetcode.com/problems/longest-consecutive-sequence) | [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence) | 🟡 Medium | Hash Set, expand only from sequence starts | `O(N)` | `O(N)` | Google, Amazon, Facebook, Microsoft (+9 more) |
| 9 | [560](https://leetcode.com/problems/subarray-sum-equals-k) | [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k) | 🟡 Medium | Prefix Sum hash map of running sums | `O(N)` | `O(N)` | Facebook, Amazon, Google, Microsoft (+16 more) |
| 10 | [525](https://leetcode.com/problems/contiguous-array) | [Contiguous Array](https://leetcode.com/problems/contiguous-array) | 🟡 Medium | Prefix sum with +1/-1 and hash map | `O(N)` | `O(N)` | Amazon, Quora, Google, Facebook (+5 more) |
| 11 | [287](https://leetcode.com/problems/find-the-duplicate-number) | [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number) | 🟡 Medium | Floyd's cycle detection on array indices | `O(N)` | `O(1)` | Amazon, Google, Microsoft, Bloomberg (+9 more) |
| 12 | [41](https://leetcode.com/problems/first-missing-positive) | [First Missing Positive](https://leetcode.com/problems/first-missing-positive) | 🔴 Hard | In-place cycle sort placing nums[i] at i-1 | `O(N)` | `O(1)` | Google, Amazon, Microsoft, Databricks (+18 more) |
| 13 | [169](https://leetcode.com/problems/majority-element) | [Majority Element](https://leetcode.com/problems/majority-element) | 🟢 Easy | Boyer-Moore Voting Algorithm | `O(N)` | `O(1)` | Amazon, Google, Microsoft, Tencent (+7 more) |
| 14 | [75](https://leetcode.com/problems/sort-colors) | [Sort Colors](https://leetcode.com/problems/sort-colors) | 🟡 Medium | Dutch National Flag 3-pointer partition | `O(N)` | `O(1)` | Microsoft, Facebook, Amazon, Google (+11 more) |
| 15 | [31](https://leetcode.com/problems/next-permutation) | [Next Permutation](https://leetcode.com/problems/next-permutation) | 🟡 Medium | Find pivot, successor, swap, reverse suffix | `O(N)` | `O(1)` | Facebook, Google, Amazon, Microsoft (+13 more) |
| 16 | [48](https://leetcode.com/problems/rotate-image) | [Rotate Image](https://leetcode.com/problems/rotate-image) | 🟡 Medium | Transpose matrix then reverse each row | `O(N^2)` | `O(1)` | Amazon, Microsoft, Google, Apple (+15 more) |
| 17 | [54](https://leetcode.com/problems/spiral-matrix) | [Spiral Matrix](https://leetcode.com/problems/spiral-matrix) | 🟡 Medium | Boundary tracking (top, bottom, left, right) | `O(M*N)` | `O(1) aux` | Microsoft, Amazon, Google, Apple (+18 more) |
| 18 | [73](https://leetcode.com/problems/set-matrix-zeroes) | [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes) | 🟡 Medium | Use first row & column as marker flags | `O(M*N)` | `O(1)` | Microsoft, Amazon, Facebook, Expedia (+8 more) |
| 19 | [189](https://leetcode.com/problems/rotate-array) | [Rotate Array](https://leetcode.com/problems/rotate-array) | 🟡 Medium | Reverse entire array, then reverse both halves | `O(N)` | `O(1)` | Microsoft, Amazon, Facebook, Bloomberg (+6 more) |
| 20 | [53](https://leetcode.com/problems/maximum-subarray) | [Maximum Subarray](https://leetcode.com/problems/maximum-subarray) | 🟡 Medium | Kadane's Algorithm (running max sum) | `O(N)` | `O(1)` | Amazon, Microsoft, Linkedin, Facebook (+31 more) |
| 21 | [152](https://leetcode.com/problems/maximum-product-subarray) | [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray) | 🟡 Medium | Maintain running min and max products | `O(N)` | `O(1)` | Linkedin, Amazon, Google, Microsoft (+8 more) |
| 22 | [121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock) | [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock) | 🟢 Easy | Track minimum price seen so far | `O(N)` | `O(1)` | Amazon, Facebook, Microsoft, Bloomberg (+34 more) |
| 23 | [122](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii) | [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii) | 🟡 Medium | Greedy capture of all positive slopes | `O(N)` | `O(1)` | Amazon, Facebook, Bloomberg, Google (+8 more) |

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
