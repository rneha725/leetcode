### Graph Related algorithms
[NeetCode Video](https://www.youtube.com/watch?v=utDu3Q7Flrw&t=3s)

#### Union Find
Combines disjoint set.

```cpp
// Union Find

unordered_map<int, int> root;

void Union(int x, int y) { //don't use `union` as it is a keyword
    int root_x = find(x);
    int root_y = find(y);
    root[root_y] = root_x; //you can be more selective here, which one will become the new root etc.
}


int find(int x) {
    if(root[x] == x) {
        return x;
    } else {
        return root[x] = find(root[x]); // VERY VERY IMPORTANT to update the root in the call stack,
        // DO NOT FORGET this line as it will disrupt the complexity.
        // As find_root will always update the root of whatver element it traverses during the search, 
        // the complexity of this becomes O(n)
    }
}

```



#### DFS
Complexity: O(n)