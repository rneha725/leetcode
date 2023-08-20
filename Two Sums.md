# Two Sums

### Question

```
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.
```

### Description
Exactly one solution means that for all other solutions, we won't find a pair.

### Solution

````cpp
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        unordered_map<int, int> lastIdxMap;

        for(int i = 0; i < n; i++) {
            lastIdxMap[nums[i]] = i;
        }

        for(int i = 0; i < n; i++) {
            int currEle = nums[i];
            int requiredEle = (target - currEle);
            int idxOfRequired = (lastIdxMap.find(requiredEle) != lastIdxMap.end())
                                ?lastIdxMap[requiredEle]:-1;
            if(idxOfRequired != -1 && idxOfRequired != i) {
                return {i, idxOfRequired};
            }
        }

        return {};
    }
};
```
````


### Complexity
#### Time: O(n)
#### Space: O(n)

----
### Variations
1. Sorted array and exactly one solution exists.
    
    https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/ 
    ````cpp 
    ```
    /* 
    two pointer solution. Take ptr1, ptr2
    time: O(n), Space: O(1)


    */

    class Solution {
    public:
        vector<int> twoSum(vector<int>& numbers, int target) {
            int n = numbers.size();
            int ptr1 = 0;
            int ptr2 = n - 1;

            while(ptr1 < ptr2) {
                int sum = numbers[ptr1] + numbers[ptr2];
                if(sum == target) {
                    return {ptr1, ptr2};
                } else if(sum > target) {
                    ptr2--;
                } else {
                    ptr1++;
                }
            }

            return {};
        }
    };```
    ````
2. 