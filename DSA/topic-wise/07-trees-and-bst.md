# 🌲 Trees & Binary Search Trees — Core Mastery Guide

> **Total Questions:** 24 &nbsp;|&nbsp; **Difficulty Breakdown:** 🟢 Easy: 7 &nbsp;|&nbsp; 🟡 Medium: 14 &nbsp;|&nbsp; 🔴 Hard: 3  
> **Mastery Goal:** Recognize the problem pattern within 60 seconds, state the invariant, and produce a bug-free O(N) or optimal solution on whiteboard/coderpad.

---

## 💡 Pattern Overview & When to Use

DFS/BFS traversals, lowest common ancestor, diameter, path sums, tree reconstruction, BST invariants, and tree serialization.

### Mental Triggers & "When to Think of Trees & Binary Search Trees"
- **Trigger 1:** Look for problem constraints ($N \le 10^5$ rules out $O(N^2)$, requires $O(N)$ or $O(N \log N)$).
- **Trigger 2:** Subarray / contiguous sequence questions often resolve to prefix hashing or sliding window.
- **Trigger 3:** If sorted or requires order preservation, look for two-pointer or binary search variants.
- **Trigger 4:** For optimization without recomputation, verify if greedy choice holds or if state transitions require dynamic programming.

---

## 🎯 Curated 250: Top Asked Questions in Trees & Binary Search Trees

| # | LC ID | Problem Title | Difficulty | Core Pattern & Approach | Optimal Time | Optimal Space | Top Asking Companies |
|---|:---:|---|:---:|---|:---:|:---:|---|
| 99 | [226](https://leetcode.com/problems/invert-binary-tree) | [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree) | 🟢 Easy | Recursive swap of left and right subtrees | `O(N)` | `O(H)` | Google, Amazon, Microsoft, Bloomberg (+4 more) |
| 100 | [104](https://leetcode.com/problems/maximum-depth-of-binary-tree) | [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree) | 🟢 Easy | DFS postorder height calculation | `O(N)` | `O(H)` | Amazon, Google, Linkedin, Facebook (+10 more) |
| 101 | [543](https://leetcode.com/problems/diameter-of-binary-tree) | [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree) | 🟢 Easy | Postorder traversal tracking max(left + right) | `O(N)` | `O(H)` | Facebook, Amazon, Google, Microsoft (+10 more) |
| 102 | [110](https://leetcode.com/problems/balanced-binary-tree) | [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree) | 🟢 Easy | Bottom-up height calculation with -1 sentinel | `O(N)` | `O(H)` | Google, Amazon, Microsoft, Facebook (+5 more) |
| 103 | [100](https://leetcode.com/problems/same-tree) | [Same Tree](https://leetcode.com/problems/same-tree) | 🟢 Easy | Simultaneous recursive validation | `O(N)` | `O(H)` | Amazon, Google, Facebook, Linkedin (+3 more) |
| 104 | [101](https://leetcode.com/problems/symmetric-tree) | [Symmetric Tree](https://leetcode.com/problems/symmetric-tree) | 🟢 Easy | Mirror DFS check (left.left == right.right) | `O(N)` | `O(H)` | Amazon, Google, Linkedin, Facebook (+16 more) |
| 105 | [572](https://leetcode.com/problems/subtree-of-another-tree) | [Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree) | 🟢 Easy | Tree serialization or recursive SameTree on nodes | `O(N * M)` | `O(H)` | Amazon, Google, Microsoft, Bloomberg (+3 more) |
| 106 | [235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree) | [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree) | 🟡 Medium | BST property: split point where p and q diverge | `O(H)` | `O(1)` | Amazon, Microsoft, Linkedin, Facebook (+7 more) |
| 107 | [236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree) | [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree) | 🟡 Medium | Postorder DFS bubbling up target discoveries | `O(N)` | `O(H)` | Amazon, Facebook, Microsoft, Linkedin (+18 more) |
| 108 | [102](https://leetcode.com/problems/binary-tree-level-order-traversal) | [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal) | 🟡 Medium | Queue BFS with level size chunking | `O(N)` | `O(W)` | Amazon, Microsoft, Linkedin, Facebook (+15 more) |
| 109 | [103](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal) | [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal) | 🟡 Medium | BFS queue with alternating deque insertions | `O(N)` | `O(W)` | Amazon, Microsoft, Facebook, Linkedin (+10 more) |
| 110 | [199](https://leetcode.com/problems/binary-tree-right-side-view) | [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view) | 🟡 Medium | BFS recording last element or DFS (node, depth) | `O(N)` | `O(W)` | Facebook, Amazon, Microsoft, Bloomberg (+14 more) |
| 111 | [1448](https://leetcode.com/problems/count-good-nodes-in-binary-tree) | [Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree) | 🟡 Medium | Preorder DFS carrying running maximum path value | `O(N)` | `O(H)` | Microsoft |
| 112 | [98](https://leetcode.com/problems/validate-binary-search-tree) | [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree) | 🟡 Medium | Inorder monotonicity or DFS with (low, high) bounds | `O(N)` | `O(H)` | Facebook, Amazon, Microsoft, Bloomberg (+20 more) |
| 113 | [230](https://leetcode.com/problems/kth-smallest-element-in-a-bst) | [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst) | 🟡 Medium | Iterative inorder traversal stopping at step k | `O(H + K)` | `O(H)` | Facebook, Google, Amazon, Microsoft (+8 more) |
| 114 | [105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal) | [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal) | 🟡 Medium | Root from preorder, divide inorder via hash map | `O(N)` | `O(N)` | Amazon, Microsoft, Facebook, Google (+9 more) |
| 115 | [106](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal) | [Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal) | 🟡 Medium | Root from postorder, divide inorder via hash map | `O(N)` | `O(N)` | Amazon, Microsoft, Facebook, Bloomberg (+2 more) |
| 116 | [124](https://leetcode.com/problems/binary-tree-maximum-path-sum) | [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum) | 🔴 Hard | Postorder max gain DFS updating global max sum | `O(N)` | `O(H)` | Facebook, Amazon, Google, Microsoft (+10 more) |
| 117 | [297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree) | [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree) | 🔴 Hard | Preorder traversal with null markers | `O(N)` | `O(N)` | Facebook, Amazon, Google, Microsoft (+17 more) |
| 118 | [450](https://leetcode.com/problems/delete-node-in-a-bst) | [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst) | 🟡 Medium | BST search, replace target with inorder successor | `O(H)` | `O(H)` | Microsoft, Oracle, Google, Amazon (+7 more) |
| 119 | [662](https://leetcode.com/problems/maximum-width-of-binary-tree) | [Maximum Width of Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree) | 🟡 Medium | BFS level order with normalized heap child indexing | `O(N)` | `O(W)` | Amazon, Bloomberg, Google, Facebook (+3 more) |
| 120 | [863](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree) | [All Nodes Distance K in Binary Tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree) | 🟡 Medium | Annotate parent pointers, graph BFS from target | `O(N)` | `O(N)` | Amazon, Facebook, Microsoft, Uber (+8 more) |
| 121 | [114](https://leetcode.com/problems/flatten-binary-tree-to-linked-list) | [Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list) | 🟡 Medium | Morris traversal or reverse postorder linking | `O(N)` | `O(1) aux` | Facebook, Bloomberg, Amazon, Microsoft (+10 more) |
| 122 | [968](https://leetcode.com/problems/binary-tree-cameras) | [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras) | 🔴 Hard | Greedy postorder state DP (covered, camera, needs-cover) | `O(N)` | `O(H)` | Google, Uber, Ebay, Facebook |

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
