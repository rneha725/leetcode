Question: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iv/

General Algorithm:

```cpp
lca(node, targets: hash_map) {
  if(node is null) return null;
  if(targets contain node) return node;

  left = lca(node->left, targets);
  right = lca(node->right, targets);

  if(left && right) return node; //if left and right of the node both subtrees contain the target elements, then return the current node
  return left ? left: right; //if only one subtree contains the target elements, then return that node which contains the targets.
} 
```
