### Step 1. Indetifying a DP problem

<details open>
<summary><h3>Step 2. Solving a DP Problem</h3></summary>
<details>
<summary><h4> Tabulation -> recommended -> Bottom to top DP </h4></summary>

  - Step 1. Come with the dimansions and the representation of each dimention. You can create a different matrix once dimesions are decided.(let's call this matrix `dp`). Note that `dp(i,j)` will solve the `problem-in-hand` for `(i,j)` cell.

  - Step 2. After that, try to create a formula for a generic index considering it is reprensenting a sub-solution.
  - Step 3. Once formula is created, try to fill up the base cases in `dp`, for example sometimes is it straightforward to solve for first row, col or diagonal elements. (for 1D dp problems it would mean solving for first or last elements. Crux is to solve for straightforward values). According to the formula, try to understand cells needs to be filled for a generic solution.
  - Step 4. Once this is done, using these filled values run the algorithm to solve for the target case, where we are trying to reach a cell in dp. (The targets could be different too, in most cases to reach that we might need to comletely fill `dp`)
  
  #### Example: [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/?envType=study-plan-v2&envId=top-interview-150)
```
Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right, which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.
```

- Step 1. For a generic solultion, we only need two dimensions, one representing row, and other, column of the input. We will create `dp(mxn)`. Here `dp(i,j)` represents, minimum path sum from top-left corner to the index `(i,j)`.
- Step 2. Use dry running the formula as many as times you want, key is to get the confidence on the formula. Formula for the problem: `dp[i][j] = min(dp[i][j - 1] + grid[i][j], dp[i - 1][j] + grid[i][j]);`
- Step 3. Base cases are the first row and column. So we have filled the first row and column with the cumulative sum from the input matrix.
- Step 4. Use the formula starting from `row=1` and `col=1` and fill matrix `dp`. Result will be `dp[m - 1][n - 1]`
</details>
<details>
<summary><h4>Recursive solution - Top to bottom</h4></summary>
Notice that `node->val` is only repreenting the current node's contribution. And operator `+` might mean any mathematical operation. 
The perfect way to right a recusive solution is to imagine a function which returns or manipulates the value we are interested in. Then imagine a recursion tree. Each function call represents a node in this tree and might be returning something. Generally the output of a function is:

`contribution of the current node + (contribution of the subtree nodes)` =>
`node->val + node->left->val + node->left->left->val.... + node->right->val + node->right->left->val...` =>
`node->val + function on the subtrees`

</details>

</details>
