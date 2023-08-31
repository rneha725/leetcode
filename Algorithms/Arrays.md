## Array Related Algorithms

### Merge Sort
### Usages

- Sorting linked lists
- Very good choice for large lists

### Properties

- Stable algorithm

### Complexity

- Time: O(n log n) → Best, Average and Worst
- Space: O(n) → a temporary array is created to merge two sorted lists.

### Code:
```cpp
//1. find the mid element
//2. sort left -> mid and mid + 1 -> right
//3. merge sorted arrays using a temporary array: merge function

void mergeSort(int left, int right) {
    if(left >= right) return;
    int mid = (left + right)/2;

    mergeSort(left, mid);
    mergeSort(mid + 1, right);

    merge(left, mid, right);
}

void merge(int left, int mid, int right) {
        //Goal: merge two sorted array left: mid and mid + 1: right
        int size = (right - left + 1);

        int i = left; //left: mid;
        int j = mid + 1; //mid+1:right;
        vector<int> tmp;

        while(i <= mid && j <= right) {
            //descending order
            int leftEle = nums[i];
            int rightEle = nums[j];

            if(leftEle <= rightEle) {
                tmp.push_back(rightEle);
                j++;
            } else {
                tmp.push_back(leftEle);
                i++;
            }
        }

        //step 2. copying remaining elements
        while(i <= mid) {
            tmp.push_back(nums[i++]);
        }

        while(j <= right) {
            tmp.push_back(nums[j++]);
        }

        //step 3. tranfering tmp values to original array
        for(int i = 0; i < tmp.size() && (i + left) < nums.size(); i++) {
            nums[i + left] = tmp[i];
        }
    }
```


