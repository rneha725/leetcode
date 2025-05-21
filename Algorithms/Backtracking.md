Generally my idea for backtracking is to use a loop and track the answer till the break condition using a param. This param will revert back to it's original values once the recursive fucntion is called.

For example for this simple question where we need to print all the permutations for the unique number array: https://leetcode.com/problems/permutations/description/
Code looked like this:

```cpp
void backtrack(vector<int>& nums, vector<int> selected) {
        if(selectNum.size() == nums.size()) { //collecting the answer once the backtrack loop needs to be ended
            res.push_back(selected);
            return;
        }
        
        for(int i = 0; i < nums.size(); i++) { //1. Using a for loop
            if(selectNum.find(nums[i]) == selectNum.end()) {
                selected.push_back(nums[i]);
                selectNum.insert(nums[i]);
                backtrack(nums, selected);
                selectNum.erase(nums[i]);  //reverting variables to its original values
                selected.pop_back();
            }
        }
    }
```
