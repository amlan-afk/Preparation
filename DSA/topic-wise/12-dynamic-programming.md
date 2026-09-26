# 📊 Dynamic Programming — Core Mastery Guide

> **Total Questions:** 26 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 2 &nbsp;|&nbsp; 🟡 Medium: 19 &nbsp;|&nbsp; 🔴 Hard: 5  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Optimal substructure and overlapping subproblems: 1D DP, 2D Grid DP, 0/1 & Unbounded Knapsack, Longest Common Subsequences, and Interval DP.

### Mental Triggers & "When to Think of Dynamic Programming"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Dynamic Programming

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 181 | [70](https://leetcode.com/problems/climbing-stairs) | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs) | 🟢 Easy | Fibonacci state transition: dp[i] = dp[i-1] + dp[i-2] | `O(N)` | `O(1)` | Goldman Sachs, Amazon, Google, Microsoft (+14 more) |
| 182 | [746](https://leetcode.com/problems/min-cost-climbing-stairs) | [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs) | 🟢 Easy | dp[i] = cost[i] + min(dp[i-1], dp[i-2]) | `O(N)` | `O(1)` | Amazon, Yahoo, Bloomberg |
| 183 | [198](https://leetcode.com/problems/house-robber) | [House Robber](https://leetcode.com/problems/house-robber) | 🟡 Medium | dp[i] = max(dp[i-1], dp[i-2] + nums[i]) | `O(N)` | `O(1)` | Google, Amazon, Adobe, Microsoft (+12 more) |
| 184 | [213](https://leetcode.com/problems/house-robber-ii) | [House Robber II](https://leetcode.com/problems/house-robber-ii) | 🟡 Medium | Break circle: max(Rob(0..N-2), Rob(1..N-1)) | `O(N)` | `O(1)` | Google, Amazon, Microsoft, Ebay |
| 185 | [337](https://leetcode.com/problems/house-robber-iii) | [House Robber III](https://leetcode.com/problems/house-robber-iii) | 🟡 Medium | Tree DP returning (rob_root, skip_root) tuple | `O(N)` | `O(H)` | Google, Amazon, Uber, Facebook |
| 186 | [5](https://leetcode.com/problems/longest-palindromic-substring) | [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring) | 🟡 Medium | Expand around center or 2D DP table | `O(N^2)` | `O(1) aux` | Amazon, Microsoft, Google, Facebook (+26 more) |
| 187 | [647](https://leetcode.com/problems/palindromic-substrings) | [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings) | 🟡 Medium | Expand around center counting valid palindromes | `O(N^2)` | `O(1)` | Facebook, Pure Storage, Amazon, Twitter (+13 more) |
| 188 | [91](https://leetcode.com/problems/decode-ways) | [Decode Ways](https://leetcode.com/problems/decode-ways) | 🟡 Medium | 1-step / 2-step DP with '01'-'26' segment checks | `O(N)` | `O(1)` | Facebook, Amazon, Google, Microsoft (+17 more) |
| 189 | [322](https://leetcode.com/problems/coin-change) | [Coin Change](https://leetcode.com/problems/coin-change) | 🟡 Medium | Unbounded Knapsack: dp[i] = min(dp[i], dp[i - coin] + 1) | `O(Amount * Coins)` | `O(Amount)` | Amazon, Microsoft, Google, Capital One (+20 more) |
| 190 | [518](https://leetcode.com/problems/coin-change-2) | [Coin Change II](https://leetcode.com/problems/coin-change-2) | 🟡 Medium | Unbounded Knapsack combinations: loop coins outer, amount inner | `O(Amount * Coins)` | `O(Amount)` | Amazon, Google, Facebook, Microsoft (+6 more) |
| 191 | [300](https://leetcode.com/problems/longest-increasing-subsequence) | [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence) | 🟡 Medium | dp array O(N^2) or Patience Sorting binary search O(N log N) | `O(N log N)` | `O(N)` | Facebook, Amazon, Google, Microsoft (+8 more) |
| 192 | [139](https://leetcode.com/problems/word-break) | [Word Break](https://leetcode.com/problems/word-break) | 🟡 Medium | dp[i] = any(dp[j] and s[j:i] in wordDict) | `O(N^2 * L)` | `O(N)` | Amazon, Facebook, Google, Microsoft (+25 more) |
| 193 | [140](https://leetcode.com/problems/word-break-ii) | [Word Break II](https://leetcode.com/problems/word-break-ii) | 🔴 Hard | Memoized DFS backtracking sentences | `O(2^N)` | `O(2^N)` | Amazon, Facebook, Google, Microsoft (+12 more) |
| 194 | [416](https://leetcode.com/problems/partition-equal-subset-sum) | [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum) | 🟡 Medium | 0/1 Knapsack for target = sum / 2 | `O(N * Target)` | `O(Target)` | Facebook, Google, Amazon, Microsoft (+6 more) |
| 195 | [494](https://leetcode.com/problems/target-sum) | [Target Sum](https://leetcode.com/problems/target-sum) | 🟡 Medium | Subset sum reduction: (sum + target) / 2 | `O(N * Target)` | `O(Target)` | Facebook, Google, Microsoft |
| 196 | [62](https://leetcode.com/problems/unique-paths) | [Unique Paths](https://leetcode.com/problems/unique-paths) | 🟡 Medium | dp[r][c] = dp[r-1][c] + dp[r][c-1] | `O(M*N)` | `O(N)` | Amazon, Google, Facebook, Bloomberg (+10 more) |
| 197 | [63](https://leetcode.com/problems/unique-paths-ii) | [Unique Paths II](https://leetcode.com/problems/unique-paths-ii) | 🟡 Medium | Grid DP with obstacle cells resetting to 0 | `O(M*N)` | `O(N)` | Amazon, Google, Facebook, Microsoft (+8 more) |
| 198 | [64](https://leetcode.com/problems/minimum-path-sum) | [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum) | 🟡 Medium | dp[r][c] = grid[r][c] + min(up, left) | `O(M*N)` | `O(N)` | Amazon, Google, Goldman Sachs, Microsoft (+9 more) |
| 199 | [1143](https://leetcode.com/problems/longest-common-subsequence) | [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence) | 🟡 Medium | 2D table: match -> 1 + diag; mismatch -> max(left, up) | `O(M*N)` | `O(min(M, N))` | Amazon, Google, Microsoft, Tencent (+3 more) |
| 200 | [72](https://leetcode.com/problems/edit-distance) | [Edit Distance](https://leetcode.com/problems/edit-distance) | 🟡 Medium | dp[i][j] = min(insert, delete, replace) + 1 | `O(M*N)` | `O(min(M, N))` | Google, Amazon, Linkedin, Microsoft (+14 more) |
| 201 | [97](https://leetcode.com/problems/interleaving-string) | [Interleaving String](https://leetcode.com/problems/interleaving-string) | 🟡 Medium | 2D boolean DP checking character matches from s1 or s2 | `O(M*N)` | `O(N)` | Amazon, Apple, Google, Uber (+4 more) |
| 202 | [312](https://leetcode.com/problems/burst-balloons) | [Burst Balloons](https://leetcode.com/problems/burst-balloons) | 🔴 Hard | Interval DP: pick last balloon burst in range [left, right] | `O(N^3)` | `O(N^2)` | Amazon, Adobe, Google, Facebook (+2 more) |
| 203 | [10](https://leetcode.com/problems/regular-expression-matching) | [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching) | 🔴 Hard | 2D DP handling '.' and '*' wildcards with zero/more copies | `O(M*N)` | `O(M*N)` | Facebook, Google, Microsoft, Amazon (+18 more) |
| 204 | [44](https://leetcode.com/problems/wildcard-matching) | [Wildcard Matching](https://leetcode.com/problems/wildcard-matching) | 🔴 Hard | 2D DP or greedy two-pointer rollback on '*' | `O(M*N) worst, O(M) avg` | `O(1) aux` | Microsoft, Google, Facebook, Amazon (+10 more) |
| 205 | [123](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii) | [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii) | 🔴 Hard | State machine with 2 transactions (buy1, sell1, buy2, sell2) | `O(N)` | `O(1)` | Amazon, Facebook, Two Sigma, Microsoft (+7 more) |
| 206 | [309](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown) | [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown) | 🟡 Medium | State machine: (held, sold, reset) transitions | `O(N)` | `O(1)` | Google, Amazon, Facebook, Apple |

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
