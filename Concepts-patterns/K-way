### Overview
---

K-way patterns generally have k sorted lists and we need to perform some operation on those. 

Common examples are, find the kth smallest element given the n sorted lists, find the smallest range containing at least one  element from n sorted lists. 

### Implementation Details
---

The implementation of the pattern is quite straightforward.

1. Create a max or min heap as per the requirement.
2. Seed the heap with the first elements of all the list. Each element might contain the vector -> {ele, ele_x_coord, ele_y_coord}
3. Process the heap as per rerquired, till it is empty.

The time complexity of heap operations depends on the heap elements, so it is generally log(n), where n is the number if lists. Consider how many times you need to do the operations.

### Example 
---
#### Problem Statement

```
Given an n x n matrix where each of the rows and columns is sorted in ascending order, return the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

You must find a solution with a memory complexity better than O(n2).
```
#### Code:
```cpp
int kthSmallest(vector<vector<int>>& matrix, int k) {
        int n = matrix.size();

        priority_queue<pair<int, pair<int, int>>, vector<pair<int, pair<int, int>>>, greater<pair<int, pair<int, int>>>> pq;

        for(int i = 0; i < n; i++) {
            pq.push({matrix[i][0], {i, 0}});
        }

        while(--k) {
            auto ele = pq.top(); pq.pop();
            auto row = ele.second.first;
            auto col = ele.second.second;
            if(col + 1 < n) {
                pq.push({matrix[row][col + 1], {row, col + 1}});
            }
        }
        auto ele = pq.top(); pq.pop();
        return ele.first;
    }

```

### Points to consider
---
- what is the nature of the sorting? non-decreasing, increasing etc.
- Defining the pq:
    Min heap: `priority_queue<type>, vector<type>, greater<type> pq;`
    Max heap: `priority_queue<type> pq;`