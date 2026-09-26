# 🪟 Sliding Window — Core Mastery Guide

> **Total Questions:** 14 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 0 &nbsp;|&nbsp; 🟡 Medium: 10 &nbsp;|&nbsp; 🔴 Hard: 4  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Crucial pattern for continuous subarrays and substring problems. Covers both fixed-size and dynamically expanding/contracting windows.

### Mental Triggers & "When to Think of Sliding Window"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Sliding Window

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 38 | [3](https://leetcode.com/problems/longest-substring-without-repeating-characters) | [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters) | 🟡 Medium | Dynamic window with last seen character index map | `O(N)` | `O(min(N, Alphabet))` | Amazon, Google, Facebook, Adobe (+29 more) |
| 39 | [424](https://leetcode.com/problems/longest-repeating-character-replacement) | [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement) | 🟡 Medium | Window length minus max frequency <= k | `O(N)` | `O(26)` | Google, Bloomberg, Pocket Gems |
| 40 | [76](https://leetcode.com/problems/minimum-window-substring) | [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring) | 🔴 Hard | Dynamic window expanding right, contracting left when valid | `O(N + M)` | `O(Alphabet)` | Facebook, Amazon, Google, Microsoft (+22 more) |
| 41 | [567](https://leetcode.com/problems/permutation-in-string) | [Permutation in String](https://leetcode.com/problems/permutation-in-string) | 🟡 Medium | Fixed-size window matching character frequency counts | `O(N)` | `O(26)` | Facebook, Amazon, Microsoft, Google (+5 more) |
| 42 | [438](https://leetcode.com/problems/find-all-anagrams-in-a-string) | [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string) | 🟡 Medium | Fixed window with character count matches | `O(N)` | `O(26)` | Facebook, Amazon, Google, Microsoft (+5 more) |
| 43 | [209](https://leetcode.com/problems/minimum-size-subarray-sum) | [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum) | 🟡 Medium | Dynamic window expanding until sum >= target, then shrink | `O(N)` | `O(1)` | Goldman Sachs, Facebook, Google, Amazon (+6 more) |
| 44 | [904](https://leetcode.com/problems/fruit-into-baskets) | [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets) | 🟡 Medium | At most 2 distinct elements sliding window | `O(N)` | `O(1)` | Google, Amazon |
| 45 | [1004](https://leetcode.com/problems/max-consecutive-ones-iii) | [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii) | 🟡 Medium | Window with at most k zeros allowed | `O(N)` | `O(1)` | Facebook, Amazon, Yandex, Microsoft |
| 46 | [239](https://leetcode.com/problems/sliding-window-maximum) | [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum) | 🔴 Hard | Monotonic decreasing deque holding indices | `O(N)` | `O(K)` | Amazon, Google, Facebook, Microsoft (+19 more) |
| 47 | [480](https://leetcode.com/problems/sliding-window-median) | [Sliding Window Median](https://leetcode.com/problems/sliding-window-median) | 🔴 Hard | Two heaps with lazy removal or multiset | `O(N log K)` | `O(K)` | Amazon, Facebook, Google, Apple (+3 more) |
| 48 | [1423](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards) | [Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards) | 🟡 Medium | Minimum sum subarray of size (N - k) | `O(N)` | `O(1)` | Google, Flipkart |
| 49 | [992](https://leetcode.com/problems/subarrays-with-k-different-integers) | [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers) | 🔴 Hard | Exact(K) = AtMost(K) - AtMost(K - 1) | `O(N)` | `O(K)` | Amazon, Google, Goldman Sachs, Alibaba (+1 more) |
| 50 | [1248](https://leetcode.com/problems/count-number-of-nice-subarrays) | [Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays) | 🟡 Medium | Sliding window / prefix odd count map | `O(N)` | `O(1)` | Roblox, Amazon |
| 51 | [713](https://leetcode.com/problems/subarray-product-less-than-k) | [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k) | 🟡 Medium | Dynamic window with running product | `O(N)` | `O(1)` | Akuna Capital, Coursera, Expedia, Google (+2 more) |

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
