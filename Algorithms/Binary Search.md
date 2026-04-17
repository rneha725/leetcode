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
    } else if(array[mid] < target) {
      left = mid + 1; //go right
    } else {
      right = mid - 1; //go left
    }
  }
  return -1;
}
```

Time complexity: O(Log(size_of_array))

## Bianry search for boundary elements
- Sometimes in a sorted space we want to search for the first/last occurence, or say an element just greater/lesser than a target element. With few tweaks we can achieve it using binary seach.

<table>
  <tr>
  <td>
    
```cpp
int binary_search(vector<int> array, int target) {
  int left = 0;
  int right = array.size();/*Use the limit [left, n) remove */

  while(left < right) {/*remove equals*/
    int mid = left + (right - left)/2;
    /*say you are looking for number >= target,
    reject_condition: array[mid] < target*/
    if(reject_condition) {         
      left = mid + 1; //go right
    } else {
      right = mid; //go left, but keep middle as a candidate
    }
  }

  return left; /*or left - 1 depending on the ask*/
}
```

</td>
<td>
  
```cpp
int binary_search(vector<int> array, int target) {
  int left = 0;
  int right = array.size() - 1;

  while(left <= right) {
    int mid = left + (right - left)/2;

    if(array[mid] == target) {
      return mid;
    } else if(array[mid] < target) {
      left = mid + 1; //go right
    } else {
      right = mid - 1; //go left
    }
  }
}
```
  
</td>
</tr>
</table

#### Defining rejcet conditions, looking for
- number >= target : array[mid] < target and return left
- number > target : array[mid] <= target and return left
- number <= target : array[mid] > target and return left - 1
- number < target : array[mid] >= target and return left - 1
