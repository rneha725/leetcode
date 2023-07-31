# Two Sums

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
            int requiredEle = (target - currEle); //-4: -1, -3
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
