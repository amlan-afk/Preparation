# 👉👈 Two Pointers — Core Mastery Guide

> **Total Questions:** 14 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 7 &nbsp;|&nbsp; 🟡 Medium: 6 &nbsp;|&nbsp; 🔴 Hard: 1  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Techniques for sorted arrays, palindrome verifications, container shrinkage, and collision detection without nested loops.

### Mental Triggers & "When to Think of Two Pointers"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Two Pointers

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 24 | [125](https://leetcode.com/problems/valid-palindrome) | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome) | 🟢 Easy | Two pointers moving inward, skipping non-alphanumeric | `O(N)` | `O(1)` | Facebook, Amazon, Microsoft, Apple (+12 more) |
| 25 | [167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted) | [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted) | 🟡 Medium | Opposite ends two-pointer convergence | `O(N)` | `O(1)` | Amazon, Google, Microsoft, Facebook (+7 more) |
| 26 | [15](https://leetcode.com/problems/3sum) | [3Sum](https://leetcode.com/problems/3sum) | 🟡 Medium | Sort array, fix one number, two pointers on remainder | `O(N^2)` | `O(1) aux` | Facebook, Amazon, Google, Microsoft (+37 more) |
| 27 | [16](https://leetcode.com/problems/3sum-closest) | [3Sum Closest](https://leetcode.com/problems/3sum-closest) | 🟡 Medium | Sort, fix one, two pointers tracking minimal gap | `O(N^2)` | `O(1)` | Amazon, Google, Adobe, Facebook (+8 more) |
| 28 | [18](https://leetcode.com/problems/4sum) | [4Sum](https://leetcode.com/problems/4sum) | 🟡 Medium | Generalized k-Sum recursion or nested 2-pointer loops | `O(N^3)` | `O(1) aux` | Amazon, Facebook, Adobe, Google (+5 more) |
| 29 | [11](https://leetcode.com/problems/container-with-most-water) | [Container With Most Water](https://leetcode.com/problems/container-with-most-water) | 🟡 Medium | Shrink boundary from smaller wall inward | `O(N)` | `O(1)` | Amazon, Google, Adobe, Facebook (+10 more) |
| 30 | [42](https://leetcode.com/problems/trapping-rain-water) | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water) | 🔴 Hard | Two pointers with leftMax and rightMax bounds | `O(N)` | `O(1)` | Amazon, Google, Facebook, Goldman Sachs (+32 more) |
| 31 | [26](https://leetcode.com/problems/remove-duplicates-from-sorted-array) | [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array) | 🟢 Easy | Slow and fast pointer overwrite | `O(N)` | `O(1)` | Microsoft, Facebook, Amazon, Google (+11 more) |
| 32 | [80](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii) | [Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii) | 🟡 Medium | Slow pointer allows at most 2 occurrences | `O(N)` | `O(1)` | Google, Amazon, Facebook, Vmware |
| 33 | [283](https://leetcode.com/problems/move-zeroes) | [Move Zeroes](https://leetcode.com/problems/move-zeroes) | 🟢 Easy | Partition non-zero elements forward | `O(N)` | `O(1)` | Facebook, Bloomberg, Google, Microsoft (+18 more) |
| 34 | [977](https://leetcode.com/problems/squares-of-a-sorted-array) | [Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array) | 🟢 Easy | Two pointers at extremes, populate from back | `O(N)` | `O(N)` | Facebook, Google, Uber, Amazon (+13 more) |
| 35 | [88](https://leetcode.com/problems/merge-sorted-array) | [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array) | 🟢 Easy | Three pointers filling backwards from end | `O(M+N)` | `O(1)` | Facebook, Microsoft, Amazon, Adobe (+21 more) |
| 36 | [680](https://leetcode.com/problems/valid-palindrome-ii) | [Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii) | 🟢 Easy | Two pointers; branch on first mismatch | `O(N)` | `O(1)` | Facebook, Microsoft, Google, Yahoo (+1 more) |
| 37 | [844](https://leetcode.com/problems/backspace-string-compare) | [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare) | 🟢 Easy | Two pointers scanning backwards with skip counters | `O(N)` | `O(1)` | Google, Facebook, Amazon, Microsoft (+3 more) |

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
