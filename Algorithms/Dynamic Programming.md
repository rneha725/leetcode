# DP Patterns + Definitions + Core Recurrence + Sample LeetCode Problems

Dynamic Programming = breaking down a problem into overlapping subproblems + storing (memoizing / tabulating) to avoid recomputation.

For each pattern:

1. Definition / when it applies
2. Key recurrence / state design skeleton
3. Example LeetCode problems (medium / hard)

---

## 1. 1D DP (Linear Sequence Decisions)

**Definition / When to use**
Problems where you're making a choice along a one-dimensional sequence (array, steps, prefix), and the state at index `i` depends on a fixed small number of previous states.

**State / Recurrence Skeleton**
Let `dp[i]` = best answer considering up to index `i` (or “ending at i”, depending on problem).
Then

```
dp[i] = f(dp[i−1], dp[i−2], …, maybe plus some cost/choice)
```

Typical “take / skip” or “extend / break” logic.

**LeetCode practice (Medium / Hard)**
Here are problems that map to 1D DP:

1. [Jump Game II (Hard / Medium variant)](https://leetcode.com/problems/jump-game-ii/)
2. [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
3. [House Robber II](https://leetcode.com/problems/house-robber-ii/)
4. [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/)
5. [Coin Change (Medium)](https://leetcode.com/problems/coin-change/)

---

## 2. 2D Grid DP

**Definition / When to use**
Problems on matrices/grids where the solution to a cell depends on its neighbors (usually above, left, sometimes diagonal or more). Typically path counting, min path sum, or constraints on movements.

**State / Recurrence Skeleton**
Let `dp[r][c] = best value (or count) to reach cell (r, c)`.
Transition often:

```
dp[r][c] = grid[r][c] + min(dp[r−1][c], dp[r][c−1])  
(or sum, or count = sum of ways from top + left)
```

You need base cases (first row, first column).

**LeetCode practice (Medium / Hard)**

1. [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
2. [Minimum Path Sum (Medium)](https://leetcode.com/problems/minimum-path-sum/)
3. [Dungeon Game (Hard)](https://leetcode.com/problems/dungeon-game/)
4. [Cherry Pickup II (Hard)](https://leetcode.com/problems/cherry-pickup-ii/)
5. [Longest Increasing Path in a Matrix (Medium / Hard)](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

---

## 3. Subsequence / String DP

**Definition / When to use**
When you operate on sequences/strings and need to find a subsequence, or compare two sequences, allowing skipping or matching. Common in LCS, edit distance, palindromic subsequences, etc.

**State / Recurrence Skeleton**
Often: `dp[i][j]` = best result using first `i` of A and first `j` of B (or same string in palindromic dp).
Transfer:

* If match: `dp[i][j] = 1 + dp[i−1][j−1]`
* Else: `dp[i][j] = max(dp[i−1][j], dp[i][j−1])`
* For edit distance: `dp[i][j] = 1 + min(dp[i−1][j], dp[i][j−1], dp[i−1][j−1])` if mismatch
* For palindromic substring / partitioning: interval / expand around center style but often DP subtype.

**LeetCode practice (Medium / Hard)**

1. [Longest Common Subsequence (Medium)](https://leetcode.com/problems/longest-common-subsequence/)
2. [Edit Distance (Hard)](https://leetcode.com/problems/edit-distance/)
3. [Distinct Subsequences (Hard)](https://leetcode.com/problems/distinct-subsequences/)
4. [Minimum Insertions to Form a Palindrome (Medium / Hard variant)](https://leetcode.com/problems/minimum-insertion-to-form-a-string/) *(or similar)*
5. [Word Break II (Hard)](https://leetcode.com/problems/word-break-ii/)

---

## 4. Knapsack / Subset DP

**Definition / When to use**
When you need to pick some items (or subset) under a constraint (sum, capacity) to maximize or satisfy something. Typical “take or not take” paradigm.

**State / Recurrence Skeleton**
Let `dp[i][w] = best achievable using first `i`items with “capacity / sum limit”`w`.
Then:

```
dp[i][w] = max(dp[i−1][w], value[i] + dp[i−1][w − weight[i]])
```

For subset-sum / boolean version:

```
dp[i][s] = dp[i−1][s] OR dp[i−1][s − arr[i]]
```

You can often optimize to 1D (reverse iterate) when memory matters.

**LeetCode practice (Medium / Hard)**

1. [Partition Equal Subset Sum (Medium)](https://leetcode.com/problems/partition-equal-subset-sum/)
2. [Target Sum (Medium)](https://leetcode.com/problems/target-sum/)
3. [Ones and Zeroes (Medium)](https://leetcode.com/problems/ones-and-zeroes/)
4. [Profitable Schemes (Hard)](https://leetcode.com/problems/profitable-schemes/)
5. [Minimum Cost to Merge Stones (Hard)](https://leetcode.com/problems/minimum-cost-to-merge-stones/)

---

## 5. Interval DP

**Definition / When to use**
Problems where the solution for an interval `[l, r]` depends on splitting it into subintervals, e.g., matrix chain multiplication, merging, bursting balloons.

**State / Recurrence Skeleton**
Define `dp[l][r]` = optimal answer for interval from `l` to `r`.
Then you try all splits:

```
dp[l][r] = min / max over k in [l, r−1] { dp[l][k] + dp[k+1][r] + cost(l, k, r) }
```

**LeetCode practice (Medium / Hard)**

1. [Burst Balloons (Hard)](https://leetcode.com/problems/burst-balloons/)
2. [Minimum Cost to Merge Stones (Hard)](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
3. [Strange Printer (Hard)](https://leetcode.com/problems/strange-printer/)
4. [Palindrome Partitioning II (Hard / tricky)](https://leetcode.com/problems/palindrome-partitioning-ii/)
5. [Minimum Score Triangulation of Polygon (Hard)](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)

---

## 6. Digit DP

**Definition / When to use**
When you’re counting or finding numbers ≤ N that satisfy some property tied to digits (sum, parity, adjacency, etc.).

**State / Recurrence Skeleton**
Typical state: `dp[pos][tight][state...]`

* `pos` = current digit index
* `tight` = whether you've matched prefix exactly or are already below
* Additional state variables (sum, last digit, count of certain things)
  Transitions: choose a digit `d` at `pos` constrained by `tight`.

**LeetCode practice (Medium / Hard)**
Digit DP problems are fewer on LeetCode, but here are some:

1. [Count Numbers with Unique Digits (Medium / Hard variant)](https://leetcode.com/problems/count-numbers-with-unique-digits/)
2. [Numbers With Repeated Digits (Hard)](https://leetcode.com/problems/numbers-with-repeated-digits/)
3. [Digit Count in Range (Hard variant)](https://leetcode.com/problems/number-of-digit-one/)
4. [Super Palindromes (Hard)](https://leetcode.com/problems/super-palindromes/)
5. [Strobogrammatic Number III (Hard)](https://leetcode.com/problems/strobogrammatic-number-iii/)

---

## 7. DP on Trees

**Definition / When to use**
When you have a tree / graph structure and you need to compute something for each node based on its children, often combining child results.

**State / Recurrence Skeleton**
You do a DFS (post-order). Let `dp[u]` represent some best result for subtree rooted at `u`, often including different states (like “take node,” “not take node”).
Then:

```
dp[u] = combine( dp[child1], dp[child2], …, with / without u logic )
```

**LeetCode practice (Medium / Hard)**

1. [House Robber III (Medium)](https://leetcode.com/problems/house-robber-iii/)
2. [Binary Tree Maximum Path Sum (Hard)](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
3. [Sum of Distances in Tree (Hard)](https://leetcode.com/problems/sum-of-distances-in-tree/)
4. [Delete Tree Node / Tree DP variants (harder ones)] *you can pick from discussion lists*
5. [Longest Univalue Path (Hard / Medium)](https://leetcode.com/problems/longest-univalue-path/)

---

## 8. Bitmask DP

**Definition / When to use**
When the state involves subsets of elements and you want to represent them compactly as bitmasks, e.g. traveling salesman, assignment, subsets with constraints.

**State / Recurrence Skeleton**
Let `dp[mask][i]` = best result for subset `mask` ending at element `i` (or covering those bits).
Then:

```
dp[mask][i] = min / max over j in mask\{i} { dp[mask without i][j] + cost(j,i) }
```

**LeetCode practice (Medium / Hard)**

1. [Traveling Salesman / bitmask style ones (LeetCode “Bitmask DP” problems) — e.g. “Minimum Cost to Visit All Points” variant]
2. [Partition to K Equal Sum Subsets (Medium / Hard)](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)
3. [Bitwise AND of Subsets / Subset enumeration ones from advanced lists]
4. [Smallest Sufficient Team (Hard)](https://leetcode.com/problems/smallest-sufficient-team/)
5. [“Assignment” / “Matching” problems using mask + DP]
