Foundational problems and how to solve them

## A DP problem where I get stuck.
- [Partition subset sum](https://leetcode.com/problems/partition-equal-subset-sum/)
- [Target Sum](https://leetcode.com/problems/target-sum/)
- 0/1 knapsack
- These problmes will give me a tree, every node will have n children. The answer I will get will reside in the leaf nodes. I can cannot optimize much but I need to explore all the solutions and find all the leaves.
- Recursion and memoization come handy and easy in these problems. However, I feel this ick in my head when I get to convert the algorithm to table format.
- Inspired by the above 2 problems, I came accross an intuition:
- so we need a matrix for this, for simplifying what should be in that matrix, think about it, we can get next level in the recursion tree and then do not care about the previous level. Likewise, we do not need to include the `i` in the table. Also, look the biggest solution space, which are the values in the leaves. So understand what is the range of leaf values and create a dp vector with the size that can contain the range.
- Once you have it, you can start from the first node, be it zero, and the start exploring the next level of the tree. From this next level you can derive the next. I have used a hashmap for this and it was quite effective.
---
One problem I see with the above, although it will work for bottom-up approach, where all the leaf nodes will reach the solution, for recursive solution we don't do that. 
If in the dp recursion tree, only the leaf will have the answer for the path taken, creating memoization will require changing the algo.
For memoization, it is essential that a node returns what is solves, basically solution of the sub-problem.
To see this, just call the recursive function for the last index and see how it behaves.
