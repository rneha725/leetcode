### Problem Statement

https://leetcode.ca/all/261.html

```
Given n nodes labeled from 0 to n-1 and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

Example 1:

Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true
Example 2:

Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false
Note: you can assume that no duplicate edges will appear in edges. Since all edges are undirected, [0,1] is the same as [1,0] and thus will not appear together in edges.
```

#### Solution:

```cpp
#include <bits/stdc++.h>
using namespace std;

unordered_set<int> visited;
unordered_map<int, vector<int>> adjList;

//1. do dfs with a source node, as the path in tree will be unique, so to reach a node, src will remain the same. If not, we can return false
bool dfs(int node, int src) {
	visited.insert(node);
	for(int nbr: adjList[node]) {
        //2. we do not need to call dfs on src again.
		if(nbr == src) continue;
        //3. If we are visiting a node again, then it is not a tree
		else if(visited.count(nbr) != 0) return false;
        //4. if some other node in the graph is not a tree, then graph is not a tree
		else {
if(dfs(nbr, node) == false) return false;
}
	}

	return true;
}

bool treeOrNot(int n, vector<vector<int>> &edges) {
	for(vector<int> edge: edges) {
		int node1 = edge[0];
		int node2 = edge[1];
		adjList[node2].push_back(node1);
		adjList[node1].push_back(node2);
	}

    //5. check if dfs from 0th node is covering all the nodes given in the edges list
	if(dfs(0, -1) == true && visited.size() == adjList.size()) {
		return true;
	}

	return false;
}


int main() {
	vector<vector<int>> vec1 = {{0,1}, {0,2}, {0,3}, {1,4}};
	vector<vector<int>> vec2 = {{0,1}, {1,2}, {2,3}, {1,3}, {1,4}};
	cout<<treeOrNot(5, vec2);
	return 0;
}
```