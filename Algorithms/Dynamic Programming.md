### DP with recursion
#### Recursion part
The perfect way to right a recusive solution is to imagine a function which returns or manipulates the value we are interested in. Then imagine a recursion tree. Each function call represents a node in this tree and might be returning something. Generally the output of a function is:

`contribution of the current node + (contribution of the subtree nodes)` =>
`node->val + node->left->val + node->left->left->val.... + node->right->val + node->right->left->val...` =>
`node->val + function on the subtrees`

Notive that `node->val` is only repreenting the current node's contribution. And operator `+` might mean any mathematical operation. 

### Tabulation
DP -> Tabulation

#### Base case
Make sure you have the base case covered to get to the leaf nodes of the tree and return.

```
Create a k-dimensional DP array, where k is the number of arguments in your top-down solution. These are the parameters that define each DP state, and the length of each dimension in the DP array should be large enough to contain all possible values.
Create a series of nested for-loops to iterate over all of the possible DP states. Make sure to adjust the direction of iteration according to the base cases, you want to visit the base case first. Sometimes this requires a little bit of critical thinking, but with practice selecting the correct order in which to iterate over the states will become easier.
Copy and paste everything from your top-down recursive function into the innermost for-loop. (Yes it is often as easy as that)
Anywhere you have return some_value change that to dp[i][j][k] = some_value and then continue. Here, i, j, and k are just the variables from your for loop that define the current dp state.
Change all calls to the recursive function from dp(0, 0, 0) to dp[0][0][0] (be mindful when you create your DP array, it makes things easier if you have each dimension be in the same order as your variables in the original recursive function, i.e. if your top down approach was dp(index, score) you want your array to be dp[index][score])
```