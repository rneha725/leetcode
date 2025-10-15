### 🧩 Backtracking — “Try everything, prune failures”

**Mental model:** brute force with smart pruning.
You explore *all possible combinations* (search tree), and backtrack when you realize a path won’t work.

**Use it when:**

* You don’t know what the final structure/solution looks like.
* You’re exploring **combinations, permutations, or subsets**.
* You can cut off invalid paths early.
* Each decision affects future ones in a branching way.

**Examples:**

* N-Queens
* Sudoku Solver
* Subset Sum / Combination Sum
* Generating permutations

**Pattern:**

```python
def backtrack(path, options):
    if path is a valid solution:
        add_to_result(path)
        return
    for option in options:
        if option is valid:
            path.append(option)
            backtrack(path, remaining_options)
            path.pop()
```

**Key idea:** It’s **exploration**, not optimization.
You’re not reusing past results — just pruning branches to make brute-force less stupid.

---

### ⚙️ Dynamic Programming — “Re-use work to optimize”

**Mental model:** recursion + caching (memorization of results).
DP *can* come from a backtracking/recursive approach, but with overlapping subproblems and optimal substructure.

**Use it when:**

* You’re solving an **optimization problem** (“max/min/count ways”) or computing **a specific value**, not enumerating all paths.
* The same subproblem repeats with different paths leading to it.
* You can express the problem as a recurrence.

**Examples:**

* Fibonacci, LIS, Knapsack, Coin Change, Edit Distance, DP on trees/grids.
* Anywhere subproblems repeat.

**Pattern:**

```python
def dp(state):
    if state in memo:
        return memo[state]
    # compute using smaller subproblems
    memo[state] = best_of(dp(smaller_states))
    return memo[state]
```

**Key idea:** It’s **optimization**, not exploration.
You don’t care about *which* path — just the *best* (or total number).

---

### 🧠 The Shortcut Heuristic

If you’re confused, ask yourself:

| Question                                                                | If Yes →     | Explanation                                        |
| ----------------------------------------------------------------------- | ------------ | -------------------------------------------------- |
| Am I exploring all possible paths until I find valid ones?              | Backtracking | You’re generating combinations/permutations.       |
| Am I trying to find max/min/count/true-false using overlapping results? | DP           | You’re reusing sub-results.                        |
| Does the problem need pruning or constraint satisfaction?               | Backtracking | It’s about feasibility, not optimization.          |
| Does the problem need caching or tabulation?                            | DP           | It’s about efficiency and overlapping subproblems. |
| Am I building a decision tree?                                          | Backtracking | Branch and bound style.                            |
| Am I filling a table or memo dictionary?                                | DP           | Reuse subproblem answers.                          |

---

### 🔄 Connection Between Them

DP often **starts as a backtracking solution** that you **optimize by adding memoization**.

Example: Fibonacci

* Backtracking version → exponential.
* Add memoization → becomes DP.

That’s literally the difference:

> Backtracking = brute force search
> DP = backtracking + memory + focus on optimal result
