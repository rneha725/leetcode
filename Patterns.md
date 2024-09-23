#### Cycle Detection in a graph
Gist is that, if we see a node again in the path being visited => cycle.

### Sliding Window

```cpp
int left = 0, right = -1;
        
while(left < n && right < n) {
    bool conditionForExpandingTheWindow;
    if(right + 1 < n && conditionForExpandingTheWindow) {
        //expand the window
        right++;
        count = max(count, right - left + 1);
    } else {
        //shrink the window
        left++;
    }
}
```