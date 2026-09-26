# 🥞 Stack & Queue — Core Mastery Guide

> **Total Questions:** 16 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 3 &nbsp;|&nbsp; 🟡 Medium: 11 &nbsp;|&nbsp; 🔴 Hard: 2  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

LIFO and FIFO structures, parsing expressions, matching parentheses, and Monotonic Stacks for next-greater/smaller element problems.

### Mental Triggers & "When to Think of Stack & Queue"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Stack & Queue

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 52 | [20](https://leetcode.com/problems/valid-parentheses) | [Valid Parentheses](https://leetcode.com/problems/valid-parentheses) | 🟢 Easy | Stack matching opening & closing brackets | `O(N)` | `O(N)` | Amazon, Facebook, Microsoft, Google (+48 more) |
| 53 | [155](https://leetcode.com/problems/min-stack) | [Min Stack](https://leetcode.com/problems/min-stack) | 🟡 Medium | Stack with parallel min tracking or (val, min) tuples | `O(1) all ops` | `O(N)` | Amazon, Bloomberg, Google, Microsoft (+20 more) |
| 54 | [150](https://leetcode.com/problems/evaluate-reverse-polish-notation) | [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation) | 🟡 Medium | Stack evaluating operand pairs on operator | `O(N)` | `O(N)` | Linkedin, Google, Amazon, Microsoft (+8 more) |
| 55 | [739](https://leetcode.com/problems/daily-temperatures) | [Daily Temperatures](https://leetcode.com/problems/daily-temperatures) | 🟡 Medium | Monotonic decreasing stack of temperatures/indices | `O(N)` | `O(N)` | Amazon, Google, Bloomberg, Linkedin (+10 more) |
| 56 | [853](https://leetcode.com/problems/car-fleet) | [Car Fleet](https://leetcode.com/problems/car-fleet) | 🟡 Medium | Sort by start pos, monotonic stack of arrival times | `O(N log N)` | `O(N)` | Google |
| 57 | [84](https://leetcode.com/problems/largest-rectangle-in-histogram) | [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram) | 🔴 Hard | Monotonic increasing stack tracking width spans | `O(N)` | `O(N)` | Amazon, Google, Microsoft, Facebook (+4 more) |
| 58 | [85](https://leetcode.com/problems/maximal-rectangle) | [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle) | 🔴 Hard | Histogram DP per row + Largest Rectangle in Histogram | `O(R * C)` | `O(C)` | Google, Amazon, Microsoft, Facebook (+9 more) |
| 59 | [394](https://leetcode.com/problems/decode-string) | [Decode String](https://leetcode.com/problems/decode-string) | 🟡 Medium | Two stacks: repeat counts and current string buffers | `O(Output Length)` | `O(N)` | Google, Bloomberg, Amazon, Facebook (+19 more) |
| 60 | [71](https://leetcode.com/problems/simplify-path) | [Simplify Path](https://leetcode.com/problems/simplify-path) | 🟡 Medium | Stack splitting by '/', handling '.' and '..' | `O(N)` | `O(N)` | Facebook, Amazon, Microsoft, Adobe (+6 more) |
| 61 | [316](https://leetcode.com/problems/remove-duplicate-letters) | [Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters) | 🟡 Medium | Monotonic stack with last-occurrence frequency map | `O(N)` | `O(26)` | Google, Amazon, Facebook, Microsoft (+5 more) |
| 62 | [402](https://leetcode.com/problems/remove-k-digits) | [Remove K Digits](https://leetcode.com/problems/remove-k-digits) | 🟡 Medium | Monotonic increasing stack dropping larger predecessors | `O(N)` | `O(N)` | Microsoft, Amazon, Google, Nutanix (+8 more) |
| 63 | [225](https://leetcode.com/problems/implement-stack-using-queues) | [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues) | 🟢 Easy | Single queue rotation on push | `O(N) push, O(1) pop` | `O(N)` | Microsoft, Amazon, Bloomberg, Mathworks (+3 more) |
| 64 | [232](https://leetcode.com/problems/implement-queue-using-stacks) | [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks) | 🟢 Easy | Two stacks (in-stack and out-stack) amortized | `O(1) amortized` | `O(N)` | Microsoft, Amazon, Google, Apple (+8 more) |
| 65 | [946](https://leetcode.com/problems/validate-stack-sequences) | [Validate Stack Sequences](https://leetcode.com/problems/validate-stack-sequences) | 🟡 Medium | Simulate pushes and greedy pops with stack | `O(N)` | `O(N)` | Google, Linkedin |
| 66 | [503](https://leetcode.com/problems/next-greater-element-ii) | [Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii) | 🟡 Medium | Circular array scan with monotonic stack (2N loop) | `O(N)` | `O(N)` | Amazon, Bloomberg, Facebook, Google (+3 more) |
| 67 | [901](https://leetcode.com/problems/online-stock-span) | [Online Stock Span](https://leetcode.com/problems/online-stock-span) | 🟡 Medium | Monotonic decreasing stack of (price, span) pairs | `O(1) amortized` | `O(N)` | Amazon, Google, Microsoft |

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
