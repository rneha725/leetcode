### Graph Related algorithms
[NeetCode Video](https://www.youtube.com/watch?v=utDu3Q7Flrw&t=3s)

#### Union Find
Combines disjoint set.

```cpp
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

### BFS

### TopSort
TopSort could also be called post order DFS as it follows DFS but before we mark the current node as visited, we need to make all of it's decendants as visited.

It works on a DAG.

- **TopSort when given graph is DAG:**
```cpp
hash_set<int> visited;
string sortedNodes;

void dfs(int node) {
    //1. Start visiting the neighbours
    for(int neighbour: graph[node]) {
        if(!visited.count(neighbourl)) {
            dfs(neighbour);
        }
    }

    //2. after we are done visiting all the neighbours, then mark a node as visited
    visited.insert(node);
    sortedNodes += to_string(node); //3. reverse this to get the order
}
```

- **TopSort with Cycle Detection:**
  Use 3 states for cycle detection as below. 

```cpp
unordered_map<int, vector<int>> adjList;
unordered_map<int, int> isVisited;
int VISITED = 1;
int BEING_VISITED = -1;
int NOT_VISITED = 0;
int CYCLE = false;
string sol;
//topSort with cycle detection
bool dfs(int node) {
	if(isVisited[node] != NOT_VISITED) {
		return isVisited[node];
	}
	
	isVisited[node] = BEING_VISITED;
	
	for(int nbr: adjList[node]) {
		if(isVisited[nbr] == NOT_VISITED) {
			if(dfs(nbr) == CYCLE) {
                return CYCLE;
            }
		}
	}
	stk.push(node) //to maintain the reverse order
	isVisited[node] = VISITED;
	return true;
}
```

### Dijstra's


### Cycle Detection
The key is to 
### Prim's or Kruskal

### Floyd Warshall

