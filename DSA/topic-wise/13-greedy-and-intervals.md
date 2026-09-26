# ⏱️ Greedy & Intervals — Core Mastery Guide

> **Total Questions:** 17 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 2 &nbsp;|&nbsp; 🟡 Medium: 14 &nbsp;|&nbsp; 🔴 Hard: 1  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Locally optimal decision making, interval mergers, meeting room schedules, jump game reachability, and gas station circuits.

### Mental Triggers & "When to Think of Greedy & Intervals"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Greedy & Intervals

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 207 | [55](https://leetcode.com/problems/jump-game) | [Jump Game](https://leetcode.com/problems/jump-game) | 🟡 Medium | Greedy max reachable index tracker | `O(N)` | `O(1)` | Amazon, Google, Facebook, Microsoft (+9 more) |
| 208 | [45](https://leetcode.com/problems/jump-game-ii) | [Jump Game II](https://leetcode.com/problems/jump-game-ii) | 🟡 Medium | BFS-like greedy interval updating jumps at boundary | `O(N)` | `O(1)` | Amazon, Google, Nutanix, Facebook (+6 more) |
| 209 | [134](https://leetcode.com/problems/gas-station) | [Gas Station](https://leetcode.com/problems/gas-station) | 🟡 Medium | Greedy reset start index when running balance drops below 0 | `O(N)` | `O(1)` | Microsoft, Amazon, Google, Ibm (+3 more) |
| 210 | [846](https://leetcode.com/problems/hand-of-straights) | [Hand of Straights](https://leetcode.com/problems/hand-of-straights) | 🟡 Medium | Min-heap or TreeMap greedy group formation | `O(N log N)` | `O(N)` | Google, Ebay, Bloomberg |
| 211 | [763](https://leetcode.com/problems/partition-labels) | [Partition Labels](https://leetcode.com/problems/partition-labels) | 🟡 Medium | Last occurrence hash map; expand window to max last pos | `O(N)` | `O(26)` | Amazon, Facebook, Apple |
| 212 | [56](https://leetcode.com/problems/merge-intervals) | [Merge Intervals](https://leetcode.com/problems/merge-intervals) | 🟡 Medium | Sort by start time; merge overlapping running interval | `O(N log N)` | `O(N)` | Facebook, Google, Amazon, Palantir Technologies (+41 more) |
| 213 | [57](https://leetcode.com/problems/insert-interval) | [Insert Interval](https://leetcode.com/problems/insert-interval) | 🟡 Medium | Add before, merge overlapping, add remaining intervals | `O(N)` | `O(N)` | Google, Facebook, Amazon, Twitter (+8 more) |
| 214 | [435](https://leetcode.com/problems/non-overlapping-intervals) | [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals) | 🟡 Medium | Sort by end time; greedily eliminate overlapping | `O(N log N)` | `O(1)` | Google, Facebook, Amazon, Bloomberg (+2 more) |
| 215 | [252](https://leetcode.com/problems/meeting-rooms) | [Meeting Rooms](https://leetcode.com/problems/meeting-rooms) | 🟢 Easy | Sort by start time; check adjacent intervals for overlap | `O(N log N)` | `O(1)` | Facebook, Amazon, Microsoft, Google (+1 more) |
| 216 | [253](https://leetcode.com/problems/meeting-rooms-ii) | [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii) | 🟡 Medium | Min-heap of end times or chronological event points | `O(N log N)` | `O(N)` | Facebook, Amazon, Google, Microsoft (+22 more) |
| 217 | [452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons) | [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons) | 🟡 Medium | Sort by end coordinate; greedy arrow placements | `O(N log N)` | `O(1)` | Facebook, Google, Amazon, Quora (+1 more) |
| 218 | [678](https://leetcode.com/problems/valid-parenthesis-string) | [Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string) | 🟡 Medium | Greedy balance tracking range [low, high] | `O(N)` | `O(1)` | Facebook, Amazon, Google, Bloomberg (+1 more) |
| 219 | [135](https://leetcode.com/problems/candy) | [Candy](https://leetcode.com/problems/candy) | 🔴 Hard | Two-pass scan (left-to-right then right-to-left peak adjustment) | `O(N)` | `O(N)` | Google, Amazon, Microsoft, Uber (+2 more) |
| 220 | [406](https://leetcode.com/problems/queue-reconstruction-by-height) | [Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height) | 🟡 Medium | Sort by height desc, k asc; insert at index k | `O(N^2)` | `O(N)` | Google, Amazon, Microsoft, Bytedance (+3 more) |
| 221 | [334](https://leetcode.com/problems/increasing-triplet-subsequence) | [Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence) | 🟡 Medium | Greedy maintenance of first and second minimums | `O(N)` | `O(1)` | Google, Facebook, Amazon, Uber (+2 more) |
| 222 | [605](https://leetcode.com/problems/can-place-flowers) | [Can Place Flowers](https://leetcode.com/problems/can-place-flowers) | 🟢 Easy | Greedy check of adjacent empty flowerbed slots | `O(N)` | `O(1)` | Linkedin, Amazon, Yahoo, Facebook (+1 more) |
| 223 | [986](https://leetcode.com/problems/interval-list-intersections) | [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections) | 🟡 Medium | Two pointers: intersect max(start) and min(end) | `O(N + M)` | `O(1) aux` | Facebook, Uber, Google, Amazon (+7 more) |

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
