## 1. Graph Related algorithms
[NeetCode Video](https://www.youtube.com/watch?v=utDu3Q7Flrw&t=3s)

- [ ] how to store a weighted graph?

### 1.1 Union Find
---

#### Type of graph: Undirected
Combines disjoint set.


In union find, one of the tricky part is to optimize it. So, for optimizing, we can try to keep the root trees as short as possible. This will optimize the find operation. 
Also, while we perform Union, we can try to connect in a such a way that the resultant tree has the minimum height. This can be done by recording the depth as Rank hashmap. If the `Rank[x] > Rank[y]` => we need to make the `y` tree as the subtree of `x` => no change in rank for x as the height is same. This is true if we exhange x and y. Although, when the heights are same, whatever us chosen as the root will have its rank increased.

The complexity of the Union-Find algorithm can be analyzed as follows: the constructor initializes the data structure requires O (N) time and space complexity. However, the time complexity required for union,connected and count is O (1).

```cpp
unordered_map<int, int> root; //1. Initialize each element as its own root
unordered_map<int, int> rank; //1. Initialize each element with depth or rank as 1

void Union(int x, int y) { //don't use `union` as it is a keyword
    int root_x = find(x);
    int root_y = find(y);
    // this is called union by rank
    if(rank[x] == rank[y]) {
        rank[root_x]++;
    }
    if(rank[x] >= rank[y]) {
        root[root_y] = root_x;
    } else {
        root[root_x] = root_y;
    }
}


int find(int x) {
    if(root[x] == x) {
        return x;
    } else {
        //this is called path compression
        return root[x] = find(root[x]); // VERY VERY IMPORTANT to update the root in the call stack,
        // DO NOT FORGET this line as it will disrupt the complexity.
        // As find_root will always update the root of whatver element it traverses during the search, 
        // the complexity of this becomes O(n)
    }
}

```

#### Application of Union Find
- In disjoin set, grpuping them using a property
- Finding cycle in Undirected connected graph


### 1.2 DFS
---
Time Complexity: O(V + E)
Space Complexity: O(V)

### 1.3 BFS
---
Time Complexity: O(V + E)
Space Complexity: O(V)

```
Note on BFS and DFS complexities: So generally there is a while loop working on nuber of vertices: V, and for each of the iteration we go through attached unvisited edges to the vertex. As we are only visiting a subset of all the edges in one iteration, the complexity can be calculated by adding the compalexities for each Vertex. Assume E is the total number of edges. For each loop:
1. 1 + E1(unvisited edges)
2. 1 + E2(...)
... till V nodes

If we will add these up: 1*V + (E1 + E2 + E3...) = (V + E)

```


### 1.4 TopSort
---

#### Type of Graph: Directed Graph

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

#### Complexity: 

Time: O(V + E). 

Space: Recursion stack: O(V)

#### Applications of TopSort
- Finding the order in which dependecies should be built

### 1.5 Shortest Path Algorithms

#### 1.5.1 [Single Source] Dijkstra's Algorithms
Type of Grapth: weighted non-negative edges
Single source to all edges. Start with an array containing distance, for the source distance is zero, else it is infinity. Push the calcualted distance in the pq.
Use a priority queue and run BFS and collect the distances.
O(V+E*LogV)

```
Weighted dirceted graph.
Graph storage: n1: [{n2, w1}...]
vector<int> djk(int src, unordered_map<int, vector<pair<int, int>>> &graph, int n) {
        vector<int> dis = vector<long long int>(n, LONG_MAX);
        vector<bool> vis = vector<bool>(n, false);

        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq; //minHeap
        pq.push({0, src});
        dis[src] = 0;

        while(!pq.empty()) {
            auto top = pq.top(); pq.pop();
            if(vis[top.second]) continue; //saves TLE -> means if we have already earlier put the nbrs in the queue, that will mean they are already present in the queue with the shorter distance.

            for(auto nbr: graph[top.second]) {
                if(vis[nbr.first] == false && dis[nbr.first] > top.first + nbr.second) {
                    dis[nbr.first] = top.first + nbr.second;
                    pq.push({dis[nbr.first], nbr.first});
                }
            }
        
            vis[top.second] = true;
        }

        return dis;
    }
```

#### 1.5.2 [Single Source] Bellman Ford

Type of graph: Weighted, negative too
This algorithm is also useful in finding the negative cycles. 
The idea is basically, if we want to have a shortest path graph from a source, the most length will be V - 1. We basically have directed edges and we calculate the minimum distance by going through all the edges V - 1 times. 
You can take an example and understand that one iteration would not generate guarateed results. Some said that if the i is the ith iteration, then we will have the tree with i as the maximum path. I do not understand it well but the key is to remember that we do the calculation V - 1 times. 

Time: O(V*E)
Space: Edge store: cost for each vertices
O(E + V)

#### Applications of Bellman Ford: 
- Detecting negative cycles

#### 1.5.3 [All Pairs] Floyd Warshall
The Floyd-Warshall algorithm solves the all-pairs shortest path problem. It uses a dynamic programming approach to do so. Negative edge weight may be present for Floyd-Warshall.

Floyd-Warshall takes advantage of the following observation: the shortest path from A to C is either the shortest path from A to B plus the shortest path from B to C or it's the shortest path from A to C that's already been found. This may seem trivial, but it's what allows Floyd-Warshall to build shortest paths from smaller shortest paths, in the classic dynamic programming way.


### 1.6 Cycle Detection
The key is to 

### 1.7 Prim's or Kruskal

#### Type of graph: Undirected connected graph

This algorithm is to get the minimum spanning tree, which is used in solving problems which require connecting components with minimum cost. 
I will discuss Kruskal's algorithm, the idea is basically to sort all the edges and then starting from the smallest edge, at the same time algorithm needs to make sure that it is not introducing a cycle, so we can use a union find algorithm while introducing a new edge in the MST. If the roots are same for two nodes connected with the edge, then no need to use this edge.

- [ ] Implementation pending

Time: Sorting: O(V*LogE)
For a graph with V vertices E edges, Kruskal's algorithm runs in O(E log V) time and Prim's algorithm can run in O(E + V log V) amortized time, if you use a Fibonacci Heap.
Prim's algorithm is significantly faster in the limit when you've got a really dense graph with many more edges than vertices. Kruskal performs better in typical situations (sparse graphs) because it uses simpler data structures.


### 1.6 LCA
Longest common ancestor in a binary tree (can be generalized to n-ary tree). It uses dfs to do this

Pseudo code:
```cpp
Ptr getLCA(root, node1, node2) {
	if(!root || root == node1 || root == node2) return root;
	Ptr left = getLCA(root->left, node1, node2);
	Ptr right = getLCA(root->right, node1, node2);
	if(left && right) return root;
	return left ? left: right;
}
```
Complexity: O(n)

Take an example to understand this.
The crux is, the subtree which does not contain either p or q will return NULL. If root is p/q, it will be retuned and consequntly, left and right will be non-null and the common root will be returned.
It is more about how two sub-trees which contain the node1 and node2 are returning the nodes in the path. And also, null is returned when subtree does not contain the nodes.

