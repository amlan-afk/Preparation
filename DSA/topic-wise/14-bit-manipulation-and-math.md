# ⚡ Bit Manipulation & Math — Core Mastery Guide

> **Total Questions:** 12 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 7 &nbsp;|&nbsp; 🟡 Medium: 5 &nbsp;|&nbsp; 🔴 Hard: 0  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Bitwise XOR tricks, Brian Kernighan bit counting, fast power squaring, integer overflow management, and prime sieves.

### Mental Triggers & "When to Think of Bit Manipulation & Math"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Bit Manipulation & Math

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 224 | [136](https://leetcode.com/problems/single-number) | [Single Number](https://leetcode.com/problems/single-number) | 🟢 Easy | XOR all elements together: x ^ x = 0 | `O(N)` | `O(1)` | Amazon, Google, Adobe, Facebook (+8 more) |
| 225 | [137](https://leetcode.com/problems/single-number-ii) | [Single Number II](https://leetcode.com/problems/single-number-ii) | 🟡 Medium | Two bitmasks (ones, twos) tracking modulo 3 frequencies | `O(N)` | `O(1)` | Google, Facebook, Amazon, Adobe |
| 226 | [191](https://leetcode.com/problems/number-of-1-bits) | [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits) | 🟢 Easy | Brian Kernighan's trick: n & (n - 1) | `O(Bits)` | `O(1)` | Microsoft, Facebook, Google, Apple (+5 more) |
| 227 | [338](https://leetcode.com/problems/counting-bits) | [Counting Bits](https://leetcode.com/problems/counting-bits) | 🟢 Easy | dp[i] = dp[i >> 1] + (i & 1) | `O(N)` | `O(1) aux` | Amazon, Facebook, Mathworks, Apple (+3 more) |
| 228 | [190](https://leetcode.com/problems/reverse-bits) | [Reverse Bits](https://leetcode.com/problems/reverse-bits) | 🟢 Easy | Bit extraction and bitwise left shift loop | `O(1)` | `O(1)` | Google, Apple, Amazon, Airbnb (+2 more) |
| 229 | [268](https://leetcode.com/problems/missing-number) | [Missing Number](https://leetcode.com/problems/missing-number) | 🟢 Easy | XOR all numbers with indices 0..n or Gauss summation | `O(N)` | `O(1)` | Amazon, Microsoft, Google, Apple (+9 more) |
| 230 | [371](https://leetcode.com/problems/sum-of-two-integers) | [Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers) | 🟡 Medium | Bitwise adder: XOR for sum, (a & b) << 1 for carry | `O(1)` | `O(1)` | Amazon, Google, Facebook, Apple (+1 more) |
| 231 | [7](https://leetcode.com/problems/reverse-integer) | [Reverse Integer](https://leetcode.com/problems/reverse-integer) | 🟡 Medium | Modulo arithmetic with INT32 overflow pre-checks | `O(log10 X)` | `O(1)` | Adobe, Google, Amazon, Bloomberg (+11 more) |
| 232 | [9](https://leetcode.com/problems/palindrome-number) | [Palindrome Number](https://leetcode.com/problems/palindrome-number) | 🟢 Easy | Reverse half of the integer digits to prevent overflow | `O(log10 X)` | `O(1)` | Adobe, Amazon, Microsoft, Google (+6 more) |
| 233 | [50](https://leetcode.com/problems/powx-n) | [Pow(x, n)](https://leetcode.com/problems/powx-n) | 🟡 Medium | Binary Exponentiation (fast power via squaring) | `O(log N)` | `O(1)` | Facebook, Linkedin, Amazon, Google (+11 more) |
| 234 | [202](https://leetcode.com/problems/happy-number) | [Happy Number](https://leetcode.com/problems/happy-number) | 🟢 Easy | Floyd's Tortoise and Hare on sum-of-squared digits | `O(log N)` | `O(1)` | Jpmorgan, Apple, Google, Amazon (+12 more) |
| 235 | [204](https://leetcode.com/problems/count-primes) | [Count Primes](https://leetcode.com/problems/count-primes) | 🟡 Medium | Sieve of Eratosthenes | `O(N log(log N))` | `O(N)` | Microsoft, Amazon, Apple, Google (+10 more) |

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
