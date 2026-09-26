# 🕸️ Graphs — Core Mastery Guide

> **Total Questions:** 23 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 0 &nbsp;|&nbsp; 🟡 Medium: 19 &nbsp;|&nbsp; 🔴 Hard: 4  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

Grid traversals (BFS/DFS), Topological Sort (Kahn's), Disjoint Set Union (Union-Find), Dijkstra's shortest paths, and Tarjan's bridges.

### Mental Triggers & "When to Think of Graphs"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Graphs

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 158 | [200](https://leetcode.com/problems/number-of-islands) | [Number of Islands](https://leetcode.com/problems/number-of-islands) | 🟡 Medium | Grid BFS/DFS or Disjoint Set Union (DSU) | `O(M*N)` | `O(M*N)` | Amazon, Facebook, Google, Microsoft (+54 more) |
| 159 | [133](https://leetcode.com/problems/clone-graph) | [Clone Graph](https://leetcode.com/problems/clone-graph) | 🟡 Medium | Hash map (original -> clone) + BFS/DFS | `O(V + E)` | `O(V)` | Facebook, Google, Amazon, Microsoft (+8 more) |
| 160 | [695](https://leetcode.com/problems/max-area-of-island) | [Max Area of Island](https://leetcode.com/problems/max-area-of-island) | 🟡 Medium | Grid DFS returning component cell area | `O(M*N)` | `O(M*N)` | Amazon, Google, Facebook, Doordash (+16 more) |
| 161 | [417](https://leetcode.com/problems/pacific-atlantic-water-flow) | [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow) | 🟡 Medium | Reverse DFS/BFS inward from both ocean borders | `O(M*N)` | `O(M*N)` | Google, Amazon, Microsoft, Facebook (+3 more) |
| 162 | [130](https://leetcode.com/problems/surrounded-regions) | [Surrounded Regions](https://leetcode.com/problems/surrounded-regions) | 🟡 Medium | Border DFS preserving connected 'O' cells, flip rest | `O(M*N)` | `O(M*N)` | Google, Amazon, Uber, Splunk (+2 more) |
| 163 | [994](https://leetcode.com/problems/rotting-oranges) | [Rotting Oranges](https://leetcode.com/problems/rotting-oranges) | 🟡 Medium | Multi-source BFS spreading infection level by level | `O(M*N)` | `O(M*N)` | Amazon, Microsoft, Adobe, Ebay (+1 more) |
| 164 | [286](https://leetcode.com/problems/walls-and-gates) | [Walls and Gates](https://leetcode.com/problems/walls-and-gates) | 🟡 Medium | Multi-source BFS outwards from all gates simultaneously | `O(M*N)` | `O(M*N)` | Facebook, Google, Amazon, Uber (+5 more) |
| 165 | [207](https://leetcode.com/problems/course-schedule) | [Course Schedule](https://leetcode.com/problems/course-schedule) | 🟡 Medium | Kahn's Topological Sort (in-degrees) or DFS cycle check | `O(V + E)` | `O(V + E)` | Amazon, Facebook, Google, Microsoft (+18 more) |
| 166 | [210](https://leetcode.com/problems/course-schedule-ii) | [Course Schedule II](https://leetcode.com/problems/course-schedule-ii) | 🟡 Medium | Kahn's algorithm appending 0 in-degree nodes to order | `O(V + E)` | `O(V + E)` | Amazon, Google, Facebook, Microsoft (+12 more) |
| 167 | [684](https://leetcode.com/problems/redundant-connection) | [Redundant Connection](https://leetcode.com/problems/redundant-connection) | 🟡 Medium | Union-Find (Disjoint Set) detecting cycle-forming edge | `O(N * alpha(N))` | `O(N)` | Google, Amazon |
| 168 | [547](https://leetcode.com/problems/friend-circles) | [Number of Provinces](https://leetcode.com/problems/friend-circles) | 🟡 Medium | Union-Find or DFS component counting | `O(N^2)` | `O(N)` | Two Sigma, Amazon, Pocket Gems, Twitter (+12 more) |
| 169 | [323](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph) | [Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph) | 🟡 Medium | Union-Find tracking component count decrement | `O(V + E * alpha(V))` | `O(V)` | Amazon, Facebook, Linkedin, Google (+2 more) |
| 170 | [261](https://leetcode.com/problems/graph-valid-tree) | [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree) | 🟡 Medium | E == V - 1 and fully connected via Union-Find/DFS | `O(V + E)` | `O(V)` | Linkedin, Amazon, Google, Facebook (+2 more) |
| 171 | [127](https://leetcode.com/problems/word-ladder) | [Word Ladder](https://leetcode.com/problems/word-ladder) | 🔴 Hard | Bidirectional BFS mutating one character per step | `O(M^2 * N)` | `O(M * N)` | Amazon, Google, Facebook, Microsoft (+22 more) |
| 172 | [399](https://leetcode.com/problems/evaluate-division) | [Evaluate Division](https://leetcode.com/problems/evaluate-division) | 🟡 Medium | Weighted directed graph BFS/DFS or Disjoint Set Union | `O((V + E) * Q)` | `O(V + E)` | Google, Amazon, Facebook, Bloomberg (+3 more) |
| 173 | [743](https://leetcode.com/problems/network-delay-time) | [Network Delay Time](https://leetcode.com/problems/network-delay-time) | 🟡 Medium | Dijkstra's Algorithm with Min-heap | `O(E log V)` | `O(V + E)` | Google, Amazon, Akuna Capital, Microsoft |
| 174 | [787](https://leetcode.com/problems/cheapest-flights-within-k-stops) | [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops) | 🟡 Medium | Bellman-Ford / BFS with step-limited relaxation | `O(K * E)` | `O(V)` | Airbnb, Amazon, Google, Facebook (+3 more) |
| 175 | [332](https://leetcode.com/problems/reconstruct-itinerary) | [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary) | 🔴 Hard | Hierholzer's Algorithm (Eulerian Path with DFS + Min-heap) | `O(E log E)` | `O(V + E)` | Uber, Google, Amazon, Facebook (+11 more) |
| 176 | [269](https://leetcode.com/problems/alien-dictionary) | [Alien Dictionary](https://leetcode.com/problems/alien-dictionary) | 🔴 Hard | Compare adjacent words, build graph, topological sort | `O(C)` | `O(U + min(U^2, N))` | Facebook, Amazon, Airbnb, Google (+13 more) |
| 177 | [310](https://leetcode.com/problems/minimum-height-trees) | [Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees) | 🟡 Medium | Prune leaves inwards layer by layer until 1 or 2 remain | `O(V)` | `O(V)` | Google, Amazon, Facebook, Snapchat (+2 more) |
| 178 | [785](https://leetcode.com/problems/is-graph-bipartite) | [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite) | 🟡 Medium | Two-color BFS/DFS checking adjacent node conflicts | `O(V + E)` | `O(V)` | Facebook, Amazon, Google, Microsoft (+3 more) |
| 179 | [886](https://leetcode.com/problems/possible-bipartition) | [Possible Bipartition](https://leetcode.com/problems/possible-bipartition) | 🟡 Medium | Graph construction from dislikes + 2-color BFS/DFS | `O(V + E)` | `O(V + E)` | Google, Facebook, Apple, Microsoft |
| 180 | [1192](https://leetcode.com/problems/critical-connections-in-a-network) | [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network) | 🔴 Hard | Tarjan's Bridge-Finding Algorithm (discovery & lowest times) | `O(V + E)` | `O(V + E)` | Amazon, Google, Microsoft, Adobe |

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
