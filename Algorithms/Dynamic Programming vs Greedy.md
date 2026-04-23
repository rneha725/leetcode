# DP vs Greedy — Recognition Guide

## Core Question

> "Does a locally optimal choice always lead to a globally optimal solution?"

- **Yes** → Greedy
- **No — past choices affect future options** → DP

---

## The Litmus Test (apply in interview)

1. **Assume greedy** — sketch the locally optimal choice
2. **Find a counterexample** — try to construct a case where greedy fails
3. **If no counterexample after 2-3 tries** → likely greedy; prove with exchange argument

> Exchange argument: *"If I swap a locally suboptimal choice for the greedy choice, does the solution get worse or stay the same?"* If always same or better → greedy is correct.

---

## The Precise Boundary

| Problem shape | Approach |
|---|---|
| Select max items, all contribute equally (+1) | Greedy |
| Select items to maximize **total value** (items have different costs/profits) | DP |
| Items have weight + value, limited capacity | DP (Knapsack) |
| Counting number of ways | DP |

**The exact moment greedy breaks:**
When you must *choose* between options and a high-value option blocks multiple lower-value options that together exceed it. Once that tradeoff exists, you need DP to evaluate both branches.

---

## Signal Patterns

| Signal in problem statement | Likely |
|---|---|
| "minimum number of steps/coins/jumps" + unit cost per step | Greedy or BFS |
| "minimum/maximum total value" with constraints | DP |
| "longest/shortest subsequence" (non-contiguous) | DP |
| "longest/shortest subarray/substring" (contiguous) | Sliding window or Greedy |
| Interval scheduling — pick most non-overlapping intervals | Greedy (sort by end) |
| Interval scheduling — maximize total profit | DP |
| Knapsack-style — items have weight + value | DP |
| "how many ways..." | DP (counting = overlapping subproblems) |

---

## Side-by-Side Examples

### Pair 1 — Interval Selection

**Greedy — 646. Maximum Length of Pair Chain**
> "Given pairs [a,b], find the longest chain where b < c for consecutive pairs."

Every selected pair contributes +1. Sort by end, greedily pick the pair with the smallest end. A smaller end never blocks more future options — no tradeoff exists.

```
pairs = [[1,2],[2,3],[3,4]]
greedy picks [1,2] → [3,4] = length 2  ✓
```

---

**DP — 1235. Maximum Profit in Job Scheduling**
> "Given jobs with start, end, profit — find max profit with no overlapping jobs."

Same structure, but each job has a profit. A high-profit job might block two lower-profit jobs that together exceed it. Greedy breaks:

```
jobs = [(1,3,50), (2,4,10), (3,5,40)]
greedy (highest profit first): picks (1,3,50) → (3,5,40) = 90
but (2,4,10) alone = 10, blocked by both above — irrelevant here
try: [(1,2,10), (1,3,50), (2,3,40)]
greedy picks (1,3,50), misses (1,2,10)+(2,3,40)=50 — tie
try: [(1,2,10), (1,3,5), (2,3,40)]
greedy picks (1,3,5), misses (1,2,10)+(2,3,40)=50  ✗
```

DP needed: `dp[i] = max(skip job i, take job i + dp[next valid job])`

---

### Pair 2 — Subsequence

**Greedy — 55. Jump Game**
> "Given jump lengths at each index, can you reach the last index?"

Track the furthest reachable index. Each step only cares about maximum reach — not which path got there. No downstream consequence to track.

```
nums = [2,3,1,1,4]
max_reach = 0 → 2 → 4 → reach end  ✓
```

---

**DP — 300. Longest Increasing Subsequence**
> "Find the length of the longest strictly increasing subsequence."

Greedy fails: including a smaller number now might allow a longer chain later — or might not. Every inclusion decision has downstream consequences.

```
nums = [3,1,2]
greedy (smallest available): picks 3, then 1 blocked (1<3) = length 1
correct: skip 3, pick 1→2 = length 2  ✗ greedy wrong
```

---

### Pair 3 — Coin / Cost Problems

**Greedy — 435. Non-overlapping Intervals**
> "Find minimum intervals to remove to make the rest non-overlapping."

Equivalent to: maximize intervals kept. Sort by end, greedily keep. All kept intervals contribute equally.

---

**DP — 322. Coin Change**
> "Minimum coins to make amount. Coins can be reused."

Greedy fails for non-canonical coin sets:

```
coins = [1,3,4], amount = 6
greedy: 4+1+1 = 3 coins
optimal: 3+3 = 2 coins  ✗ greedy wrong
```

DP needed because picking a large coin now may force expensive choices later.

---

### Pair 4 — Partition / Subset

**Greedy — 763. Partition Labels**
> "Partition string so each letter appears in at most one part. Maximize number of parts."

For each character, track its last occurrence. Greedily extend the current partition boundary. No value tradeoff — all partitions are equally good.

---

**DP — 416. Partition Equal Subset Sum**
> "Can you partition array into two subsets with equal sum?"

Greedy fails immediately — you can't decide locally whether to include a number without knowing future numbers. Boolean reachability at each sum requires tracking all possible subset sums.

---

## Summary

```
Equal contribution per item?
├── YES → Greedy (sort by some key, scan once)
└── NO (items have varying value/cost)
    ├── Choices interact / overlap? → DP
    └── Choices independent? → Greedy still possible
```

**When in doubt:** try to construct a counterexample for greedy with ~3 items. If you can't, greedy is likely correct. If you can, reach for DP.
