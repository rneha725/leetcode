Read: [Backtracking Notes](../Algorithms/Backtracking.md)
<details><summary><h3>Letter Combinations of a Phone Number</h3></summary>
<a href="https://leetcode.com/problems/letter-combinations-of-a-phone-number/description">LeetCode Link</a>


**Problem Statement**
----
Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.


**Solution**
----
- Step 1. Identifying the problem: As we need to go through all the values without any hope of optimization, this looks like a backtracking problem.,
- Step 2. As backtracking problems require a recursive solution, which means a solution which goes top-to-down. We need to employ a dfs like code to solve this. Built a recursive function which takes an index, inside the body solving for an index require solving for the next index first. Did that and then recurse on the solution retunred to prepend the digit's character mapping.
- Step 3. Catching the edge cases: 
    1. empty string input. 
    2. whule backtracking if we reach end of digits array
- Step 4. Complexity Analysis: as the maximum digits provided cannot be more that 4, so it cannot be more that (4^4). Space: same as the time complexity: number of maximum solutions.
</details>
