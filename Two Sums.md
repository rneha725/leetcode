# Two Sums

### Question

```
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.
```

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