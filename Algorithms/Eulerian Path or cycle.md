Resource : https://algorithms.discrete.ma.tum.de/graph-algorithms/hierholzer/index_en.html

## Eulerian path:
Path in the graph which covers all the edge exactly once. Given a graph, a eulerian path would translate 
into an array. Using this, we can say two things about the graph which contains eulerian path:
 -> array: [x,x,x,x,x,x]
- For undirected graph: there will be exactly 2 nodes having odd degree and other nodes will have even degree
- For directed graph: one node will have 1 more out degree than the ingree(first node in array), and one node
will have one more in degree than out degree(last node).

Once we have validated this, we can move onto solving this.
Finding the path from the left node.

```cpp
void visit(string airport) {
    while (targets[airport].size()) {
        string next = *targets[airport].begin();
        targets[airport].erase(targets[airport].begin());
        visit(next);
    }
    route.push_back(airport);
}
```

take an example where we have built the solution array and now we are going through the array.

Questions:

https://leetcode.com/problems/reconstruct-itinerary
