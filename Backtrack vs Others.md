```cpp
void backtrackingDFS(Coord c, int cnt, vector<vector<int>>& grid) {
    vis[c] = true;
    for(Coord cd: directions) {
        //logic
    }
    vis[c] = false; <------------------- this is a backtrack
}
```
Normal dfs would not work here
