# 🔗 Linked List — Core Mastery Guide

> **Total Questions:** 16 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 5 &nbsp;|&nbsp; 🟡 Medium: 9 &nbsp;|&nbsp; 🔴 Hard: 2  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Pointer manipulation, fast & slow pointer cycle detection, in-place list reversal, k-group operations, and deep copying with random pointers.

### Mental Triggers & "When to Think of Linked List"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Linked List

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 83 | [206](https://leetcode.com/problems/reverse-linked-list) | [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list) | 🟢 Easy | Iterative 3-pointer (prev, curr, next) reversal | `O(N)` | `O(1)` | Amazon, Microsoft, Facebook, Google (+30 more) |
| 84 | [92](https://leetcode.com/problems/reverse-linked-list-ii) | [Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii) | 🟡 Medium | Partial subsegment reversal with dummy node | `O(N)` | `O(1)` | Amazon, Microsoft, Facebook, Adobe (+6 more) |
| 85 | [25](https://leetcode.com/problems/reverse-nodes-in-k-group) | [Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group) | 🔴 Hard | Count k nodes, reverse segment, recursively connect | `O(N)` | `O(1)` | Microsoft, Amazon, Facebook, Mathworks (+9 more) |
| 86 | [21](https://leetcode.com/problems/merge-two-sorted-lists) | [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists) | 🟢 Easy | Dummy head pointer with iterative comparison | `O(N + M)` | `O(1)` | Amazon, Microsoft, Adobe, Facebook (+25 more) |
| 87 | [23](https://leetcode.com/problems/merge-k-sorted-lists) | [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists) | 🔴 Hard | Min-heap of node heads or divide-and-conquer merge | `O(N log K)` | `O(K)` | Amazon, Facebook, Google, Microsoft (+31 more) |
| 88 | [141](https://leetcode.com/problems/linked-list-cycle) | [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle) | 🟢 Easy | Floyd's Tortoise and Hare pointers | `O(N)` | `O(1)` | Microsoft, Amazon, Google, Adobe (+10 more) |
| 89 | [142](https://leetcode.com/problems/linked-list-cycle-ii) | [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii) | 🟡 Medium | Floyd's cycle detection + head meeting point | `O(N)` | `O(1)` | Microsoft, Amazon, Adobe, Google (+3 more) |
| 90 | [143](https://leetcode.com/problems/reorder-list) | [Reorder List](https://leetcode.com/problems/reorder-list) | 🟡 Medium | Find middle, reverse second half, weave two halves | `O(N)` | `O(1)` | Facebook, Amazon, Microsoft, Google (+7 more) |
| 91 | [19](https://leetcode.com/problems/remove-nth-node-from-end-of-list) | [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list) | 🟡 Medium | Two pointers separated by n steps + dummy node | `O(N)` | `O(1)` | Amazon, Microsoft, Facebook, Google (+9 more) |
| 92 | [138](https://leetcode.com/problems/copy-list-with-random-pointer) | [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer) | 🟡 Medium | Interleaved node duplication or hash map lookup | `O(N)` | `O(1) aux` | Amazon, Microsoft, Facebook, Bloomberg (+12 more) |
| 93 | [2](https://leetcode.com/problems/add-two-numbers) | [Add Two Numbers](https://leetcode.com/problems/add-two-numbers) | 🟡 Medium | Elementary math addition with carry propagation | `O(max(N, M))` | `O(1) aux` | Amazon, Google, Microsoft, Adobe (+33 more) |
| 94 | [148](https://leetcode.com/problems/sort-list) | [Sort List](https://leetcode.com/problems/sort-list) | 🟡 Medium | Merge sort on linked list with fast/slow mid finding | `O(N log N)` | `O(log N) or O(1)` | Amazon, Microsoft, Facebook, Google (+2 more) |
| 95 | [160](https://leetcode.com/problems/intersection-of-two-linked-lists) | [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists) | 🟢 Easy | Two pointers swapping heads on end of list | `O(N + M)` | `O(1)` | Microsoft, Amazon, Bloomberg, Linkedin (+16 more) |
| 96 | [234](https://leetcode.com/problems/palindrome-linked-list) | [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list) | 🟢 Easy | Find middle, reverse second half, compare equality | `O(N)` | `O(1)` | Amazon, Microsoft, Bloomberg, Adobe (+10 more) |
| 97 | [61](https://leetcode.com/problems/rotate-list) | [Rotate List](https://leetcode.com/problems/rotate-list) | 🟡 Medium | Compute length, make circular ring, break at (k % len) | `O(N)` | `O(1)` | Microsoft, Amazon, Linkedin, Adobe (+2 more) |
| 98 | [328](https://leetcode.com/problems/odd-even-linked-list) | [Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list) | 🟡 Medium | Odd and even pointer advance and link weave | `O(N)` | `O(1)` | Capital One, Microsoft, Amazon, Bloomberg (+3 more) |

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
