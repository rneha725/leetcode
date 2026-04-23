# DP vs Greedy — Interview Reference

---

## What is Greedy?

Make the **locally optimal choice at each step**, never looking back or reconsidering.

- No storage of subproblems
- Single pass (usually)
- Works when local optima compound into a global optimum

**Mental model:** *"Take the best available option right now, trust it leads to the best overall answer."*

---

## What is Dynamic Programming?

Break the problem into **overlapping subproblems**, solve each once, store the result, and **combine** them to build the answer.

- Requires optimal substructure: the optimal answer to the full problem uses optimal answers to subproblems
- Requires overlapping subproblems: the same subproblem recurs
- Trades space for time (memoization / tabulation)

**Mental model:** *"What's the best answer for this smaller version? Build on it."*

---

## Core Difference

| | Greedy | DP |
|---|---|---|
| Decisions | Irrevocable | Can explore all options |
| Looks back? | No | Yes — uses stored subproblem results |
| Correctness proof | Exchange argument / stays-ahead | Optimal substructure + overlapping subproblems |
| Complexity | Usually O(n log n) or O(n) | Usually O(n²) or O(n·W) |
| When it works | Local = global optimum | Local ≠ global optimum |

---

## When to Choose Greedy

Ask yourself: **"If I always pick the best available option, does it provably never hurt me later?"**

Signals that greedy will work:
- The problem asks: can you reach X? (not minimum cost to reach X)
- Choices are independent — making one choice doesn't poison future choices
- You can prove by **exchange argument**: swapping any greedy choice for another only makes things worse or equal
- Classic structures: intervals, scheduling, always-sorted selection

**Greedy-friendly problem types:**
- Interval scheduling / activity selection
- Minimum spanning tree (Kruskal, Prim)
- Huffman encoding
- Jump reachability (can you reach the end?)

---

## When to Choose DP

Ask yourself: **"Does an earlier choice affect what options I have later?"**

Signals that DP is needed:
- Problem asks for **count**, **min cost**, **max profit**, or **number of ways**
- Greedy gives wrong answer on a crafted counterexample
- You need to consider multiple futures branching from the same state
- The word "minimum", "maximum", "how many ways" appears

**DP-friendly problem types:**
- Knapsack variants
- Shortest/cheapest path with constraints
- Sequence alignment (LCS, edit distance)
- Partition problems
- Counting paths / combinations

---

## The Decision Flowchart

```
Does the problem ask for a yes/no or just reachability?
    └── YES → Try greedy first

Does making a choice now limit future choices in a complex way?
    └── YES → DP

Can you prove: always picking the local best never blocks the global best?
    └── YES → Greedy
    └── NO / UNSURE → DP

Does the problem have "min/max/count/how many ways"?
    └── YES → Strong DP signal
```

---

## Key LeetCode Problems

### Greedy
| Problem | Core Idea |
|---|---|
| 455 — Assign Cookies | Sort both; match greedily |
| 55 — Jump Game | Track max reachable index |
| 435 — Non-overlapping Intervals | Sort by end time; keep earliest-ending |
| 134 — Gas Station | If total gas ≥ total cost, a solution exists; track running sum |
| 763 — Partition Labels | Last occurrence of each char; extend window greedily |

### DP
| Problem | Core Idea |
|---|---|
| 322 — Coin Change | `dp[i] = min(dp[i - coin] + 1)` for all coins |
| 300 — Longest Increasing Subsequence | `dp[i] = max(dp[j] + 1)` for all j < i where arr[j] < arr[i] |
| 416 — Partition Equal Subset Sum | Knapsack: can we hit sum/2? |
| 1143 — LCS | Classic 2D DP on two sequences |
| 72 — Edit Distance | `dp[i][j]` = min ops to convert s1[:i] → s2[:j] |

---

## The Bridge Problem: Greedy → DP

### Jump Game I (LC 55) — Greedy works ✅

**Problem:** Given array `nums`, where `nums[i]` is the max jump from index `i`, return `true` if you can reach the last index.

**Why greedy works:** You only care about reachability, not cost. Tracking the furthest reachable index is sufficient.

```cpp
bool canJump(vector<int>& nums) {
    int maxReach = 0;
    for (int i = 0; i < nums.size(); i++) {
        if (i > maxReach) return false;      // can't even get here
        maxReach = max(maxReach, i + nums[i]);
    }
    return true;
}
```

**Why it works:** At each index, update the furthest you can reach. If you ever step onto an index beyond your current reach, it's unreachable. No subproblem storage needed — one number (`maxReach`) carries everything.

---

### Now modify the problem: minimum jumps required

### Jump Game II (LC 45) — Greedy still works, but the reasoning shifts ⚠️

**Problem:** Same setup. Now return the **minimum number of jumps** to reach the last index.

**Greedy still works here** — but the reason is more subtle. You treat it like BFS levels:
- `curEnd` = the furthest you can reach with the current number of jumps
- `farthest` = the furthest you can reach from any index in this level

```cpp
int jump(vector<int>& nums) {
    int jumps = 0, curEnd = 0, farthest = 0;
    for (int i = 0; i < nums.size() - 1; i++) {
        farthest = max(farthest, i + nums[i]);
        if (i == curEnd) {          // exhausted current jump's reach
            jumps++;
            curEnd = farthest;
        }
    }
    return jumps;
}
```

**Why greedy still works:** Every position reachable in k jumps is a contiguous prefix. There's no scenario where skipping a closer landing spot saves you jumps later — always extending to the farthest costs nothing.

---

### Now break greedy: add a cost per position

### Jump Game VII variant / Weighted Jump (DP required) ❌ Greedy fails

**Modified problem:** Each index `i` has a **cost** `cost[i]`. You can jump 1 to `nums[i]` steps from index `i`. Find the **minimum total cost** to reach the last index.

**Why greedy breaks:** The farthest-reach heuristic ignores cost. A far jump might land on an expensive index; a shorter jump might reach a cheap one that sets up a better path.

**Counterexample:**
```
nums = [3, 1, 1, 1]
cost = [0, 100, 1, 1]

Greedy (farthest): jump from 0 → index 3 directly. Total cost = 0 + 1 = 1. ✓ (happens to work here)

nums = [2, 2, 1, 1]
cost = [0, 1, 100, 1]

Greedy: 0 → 2 (cost 100) → 3 (cost 1). Total = 101
DP:     0 → 1 (cost 1) → 3 (cost 1). Total = 2  ✓
```

**DP solution:**

```cpp
int minCostJump(vector<int>& nums, vector<int>& cost) {
    int n = nums.size();
    vector<int> dp(n, INT_MAX);
    dp[0] = cost[0];

    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            // can we jump from j to i?
            if (j + nums[j] >= i && dp[j] != INT_MAX) {
                dp[i] = min(dp[i], dp[j] + cost[i]);
            }
        }
    }

    return dp[n - 1];
}
```

**Recurrence:** `dp[i] = min over all j that can reach i: dp[j] + cost[i]`

**Why DP is needed now:** The "best" jump from index 0 depends on what the costs look like further ahead. A locally bad choice (short jump to a cheap index) can enable a globally better path. Greedy's irrevocability becomes a liability.

---

## The Core Lesson from This Progression

| Version | Question | Algorithm | Why |
|---|---|---|---|
| Can you reach the end? | Reachability | Greedy | Only the frontier matters |
| Min jumps to reach end? | Count (uniform cost) | Greedy (BFS-like) | Extending farthest is always optimal |
| Min cost to reach end? | Cost (non-uniform) | DP | Future costs break greedy's local optimality |

**Rule of thumb:** The moment costs become non-uniform across choices, greedy almost always breaks. That's your signal to switch to DP.

---

## One-Line Memory Anchors

- **Greedy:** "Best now = best overall" — provable, not just intuitive
- **DP:** "Best overall = best subproblem + one more step"
- **Greedy fails when:** future costs are non-uniform or choices constrain each other
- **DP recurrence first:** write `dp[i] = f(dp[i - something])` before writing any code
