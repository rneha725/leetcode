# Dynamic Programming Master Notes – Augmented

Dynamic Programming = breaking problems into overlapping subproblems + storing results to avoid recomputation.

Each pattern below has:

* **Definition** – when it applies
* **State / Recurrence Skeleton**
* **How to solve** – steps, recursion vs tabulation
* **Code skeleton** – minimal template
* **LeetCode Practice** – 5 medium/hard problems

---

## 1. **1D DP – Linear Sequence Decisions**

**Definition**
Problems along a linear sequence where state at `i` depends on 1–2 (or few) previous states. Examples: Fibonacci, climbing stairs, coin change.

**State / Recurrence**

```
dp[i] = f(dp[i-1], dp[i-2], …)
```

**How to Solve**

1. Define `dp[i]` = best answer up to index `i`.
2. Base cases: `dp[0]`, maybe `dp[1]`.
3. Transition: depends on last 1–2 elements.
4. Approach: small n → recursion+memo, else tabulation. Optimize space if needed.

**Code Skeleton**

```python
dp[0] = base
for i in range(1,n):
    dp[i] = f(dp[i-1], dp[i-2], ...)
return dp[n-1]
```

**LeetCode Practice**

* [Jump Game II](https://leetcode.com/problems/jump-game-ii/)
* [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
* [House Robber II](https://leetcode.com/problems/house-robber-ii/)
* [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/)
* [Coin Change](https://leetcode.com/problems/coin-change/)

---

## 2. **2D Grid DP**

**Definition**
Problems on grids/matrices; solution of a cell depends on neighbors (usually top/left). Examples: unique paths, min path sum, obstacle grid.

**State / Recurrence**

```
dp[r][c] = grid[r][c] + f(dp[r-1][c], dp[r][c-1])
```

**How to Solve**

1. State = `dp[r][c]` = best answer to reach `(r,c)`.
2. Base: first row/column.
3. Transition: top, left, maybe diagonal.
4. Approach: tabulation preferred; memoization works for DFS style.

**Code Skeleton**

```python
dp[0][0] = grid[0][0]
for r in range(rows):
    for c in range(cols):
        if r>0: dp[r][c] = max(dp[r][c], dp[r-1][c]+grid[r][c])
        if c>0: dp[r][c] = max(dp[r][c], dp[r][c-1]+grid[r][c])
return dp[rows-1][cols-1]
```

**LeetCode Practice**

* [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
* [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
* [Dungeon Game](https://leetcode.com/problems/dungeon-game/)
* [Cherry Pickup II](https://leetcode.com/problems/cherry-pickup-ii/)
* [Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

---

## 3. **Subsequence DP (LIS / LCS / Edit Distance)**

**Definition**
Problems on sequences/strings: subsequence comparisons, palindromes, edit distance.

**State / Recurrence**

```
dp[i][j] = if match: dp[i-1][j-1]+1
           else: max(dp[i-1][j], dp[i][j-1])
```

For edit distance: consider insert/delete/replace.

**How to Solve**

1. Define `dp[i][j]` = best result for first `i` of A and `j` of B.
2. Base: `dp[0][*]=0`, `dp[*][0]=0`.
3. Approach: small n → recursion+memo, else tabulation preferred.
4. For LIS: O(n²) DP or O(n log n) with binary search.

**Code Skeleton**

```python
for i in range(1,n+1):
    for j in range(1,m+1):
        if A[i-1] == B[j-1]:
            dp[i][j] = dp[i-1][j-1]+1
        else:
            dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

**LeetCode Practice**

* [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
* [Edit Distance](https://leetcode.com/problems/edit-distance/)
* [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
* [Minimum Insertions to Form a Palindrome](https://leetcode.com/problems/minimum-insertion-to-form-a-string/)
* [Word Break II](https://leetcode.com/problems/word-break-ii/)

---

## 4. **Knapsack / Subset DP**

**Definition**
Pick items under constraints to maximize/minimize. Examples: 0/1 knapsack, subset sum, partition problem.

**State / Recurrence**

```
dp[i][w] = max(dp[i-1][w], val[i]+dp[i-1][w-wt[i]])
```

**How to Solve**

1. Define state.
2. Base: `dp[0][*]=0` (or False).
3. Approach: tabulation standard; recursion+memo possible.
4. Can optimize space to 1D for large capacity.

**Code Skeleton**

```python// given N, maxWeight, weights and values
long[,] d = new long[N + 1, maxWeight + 1];
for (long i = 0; i < N; i++) {
	for (long w = 0; w <= maxWeight; w++) {                    
		if (weights[i] <= w) {
			// Exclude or include
			d[i + 1, w] = Math.Max(d[i, w], d[i, w - weights[i]] + values[i]);
		}
		else {
			// Exclude
			d[i + 1, w] = d[i, w];
		}                    
	}
}
```

Reference: [Link](https://leetcode.com/discuss/post/1152328/01-knapsack-problem-and-dynamic-programm-4had/)

**LeetCode Practice**

* [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
* [Target Sum](https://leetcode.com/problems/target-sum/)
* [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)
* [Profitable Schemes](https://leetcode.com/problems/profitable-schemes/)
* [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)

---

## 5. **Interval DP**

**Definition**
Solution for interval `[l,r]` depends on splitting it. Examples: matrix chain multiplication, burst balloons.

**State / Recurrence**

```
dp[l][r] = min/max(dp[l][k]+dp[k+1][r]+cost)
```

**How to Solve**

1. Define state.
2. Base: `dp[l][l] = single element cost`.
3. Solve smaller intervals first.
4. Approach: iterative tabulation preferred.

**Code Skeleton**

```python
for length in range(2,n+1):
    for l in range(0,n-length+1):
        r = l+length-1
        dp[l][r] = INF
        for k in range(l,r):
            dp[l][r] = min(dp[l][r], dp[l][k]+dp[k+1][r]+cost(l,k,r))
```

**LeetCode Practice**

* [Burst Balloons](https://leetcode.com/problems/burst-balloons/)
* [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
* [Strange Printer](https://leetcode.com/problems/strange-printer/)
* [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/)
* [Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)

---

## 6. **Digit DP**

**Definition**
Counting numbers ≤ N satisfying digit constraints (sum, adjacency, parity).

**State / Recurrence**

```
dp[pos][tight][state...]
```

**How to Solve**

1. Define `pos, tight, extra_state`.
2. Base: `pos==len(digits)` → return 1/0.
3. Try all digits allowed at pos.
4. Approach: recursion + memoization.

**Code Skeleton**

```python
def dfs(pos, tight, state):
    if pos==len(digits): return 1
    if memo[pos][tight][state]!=-1: return memo[pos][tight][state]
    limit = digits[pos] if tight else 9
    res = 0
    for d in range(0,limit+1):
        res += dfs(pos+1, tight and d==limit, new_state)
    memo[pos][tight][state] = res
    return res
```

**LeetCode Practice**

* [Count Numbers with Unique Digits](https://leetcode.com/problems/count-numbers-with-unique-digits/)
* [Numbers With Repeated Digits](https://leetcode.com/problems/numbers-with-repeated-digits/)
* [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/)
* [Super Palindromes](https://leetcode.com/problems/super-palindromes/)
* [Strobogrammatic Number III](https://leetcode.com/problems/strobogrammatic-number-iii/)

---

## 7. **DP on Trees**

**Definition**
Compute answers for tree nodes based on children’s DP. Examples: tree diameter, max path sum, independent set.

**State / Recurrence**

```
dp[u] = f(dp[child1], dp[child2], ...)
```

**How to Solve**

1. DFS post-order.
2. Compute dp[u] from children.
3. Multiple states if “take / not take node”.
4. Approach: recursion + memoization.

**Code Skeleton**

```python
def dfs(u,parent):
    dp[u] = 0 # or [take,not_take]
    for v in graph[u]:
        if v!=parent:
            dfs(v,u)
            dp[u]+=f(dp[v])
```

**LeetCode Practice**

* [House Robber III](https://leetcode.com/problems/house-robber-iii/)
* [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
* [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/)
* [Longest Univalue Path](https://leetcode.com/problems/longest-univalue-path/)
* [Delete Tree Node variants] (discussion / advanced problems)

---

## 8. **Bitmask DP**

**Definition**
State represents subsets with a mask. Examples: traveling salesman, assignment problem, subset constraints.

**State / Recurrence**

```
dp[mask][i] = max/min(dp[mask^(1<<i)][j]+cost[j][i])
```

**How to Solve**

1. Base: single elements mask.
2. Try adding elements to mask from previous subsets.
3. Approach: iterative preferred; memoization works.

**Code Skeleton**

```python
for mask in range(1,1<<n):
    for i in range(n):
        if mask & (1<<i):
            for j in range(n):
                if mask & (1<<j) and i!=j:
                    dp[mask][i] = max(dp[mask][i], dp[mask^(1<<i)][j]+cost[j][i])
```

**LeetCode Practice**

* [Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/)
* [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)
* [Assignment / matching DP problems] (discussion)
* [Traveling Salesman / bitmask DP]
* [Other subset enumeration bitmask problems]

---

### ✅ Quick Recap – Approach Summary

| Pattern     | Approach         | Notes                            |
| ----------- | ---------------- | -------------------------------- |
| 1D DP       | Tabulation       | Memo works for small n           |
| 2D Grid     | Tabulation       | DFS + memo possible              |
| Subsequence | Tabulation       | Memo O(n²)                       |
| Knapsack    | Tabulation       | Space optimized to 1D            |
| Interval    | Tabulation       | Solve increasing interval length |
| Digit       | Recursion + Memo | Tabulation rare                  |
| Tree        | Recursion        | Post-order DFS                   |
| Bitmask     | Iterative / Memo | Enumerate masks                  |


