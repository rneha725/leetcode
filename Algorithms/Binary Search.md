## Normal binary search
- Sorted search space where we know that element exists in the seach space

```cpp
int binary_search(vector<int> array, int target) {
  int left = 0;
  int right = array.size() - 1;

  while(left <= right) {
    int mid = left + (right - left)/2;

    if(array[mid] == target) {
      return mid;
    } else if(array[mid] > target) {
      right = mid - 1; //go left
    } else {
      left = mid + 1; //go right
    }
  }
}
```

Time complexity: O(Log(size_of_array))
