### Given an integer array nums, return the length of the longest strictly increasing subsequence.

Example:
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.

```cpp
int lengthOfLIS(vector<int>& nums) {
        vector<int> res;

        res.push_back(nums[0]);

        for(int i = 1; i < nums.size(); i++) {
            if(res.back() < nums[i]) res.push_back(nums[i]);
            else {
                int it = lower_bound(res.begin(), res.end(), nums[i]) - res.begin();
                res[it] = nums[i];
            }
        }

        return res.size();
    }
```

This only returns the length of the LIS and not the LIS itself
