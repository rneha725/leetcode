## Merge Sort:
All time complexities are O(n*logn)
Space:
    - Stack space: O(log n)
    - Temporary array: each step requires temp array : O(log n)

### Code

```
class Solution {
public:
    void merge(vector<int>& nums, int leftStart, int rightStart, int end) {
        vector<int> temp = vector<int>(end - leftStart + 1);

        int ptr1 = leftStart, ptr2 = rightStart, ptr = 0;
        while(ptr1 <= rightStart - 1 && ptr2 <= end) {
            if(nums[ptr1] <= nums[ptr2]) {
                temp[ptr++] = nums[ptr1++];
            } else {
                temp[ptr++] = nums[ptr2++];
            }
        }

        while(ptr1 <= rightStart - 1) {
            temp[ptr++] = nums[ptr1++];
        }

        while(ptr2 <= end) {
            temp[ptr++] = nums[ptr2++];
        }

        ptr = leftStart;
        for(int i = 0; i < temp.size(); i++) {
            nums[leftStart++] = temp[i];
        }
    }

    void mergeSort(vector<int>& nums, int start, int end) {
        if(start < end) {
            int mid = (start + end)/2;
            mergeSort(nums, start, mid);
            mergeSort(nums, mid + 1, end);

            merge(nums, start, mid + 1, end);
        }
    }

    vector<int> sortArray(vector<int>& nums) {
        mergeSort(nums, 0, nums.size() - 1);
        return nums;
    }
};
```