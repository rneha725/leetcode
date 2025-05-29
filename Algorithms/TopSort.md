Top sort is needed when you have a dependency graph and you need to start resolving the dependencies starting with the node, which is not dependent on the
other, the resolving the direct dependency of the first set of nodes, once second set is resolved, then moved to the third set of nodes and so on.
Top Sort only works for directed acylic graphs.

A good example of top sort is this:

```
You are given a replacements mapping and a text string that may contain placeholders formatted as %var%, where each var corresponds to a key in the
replacements mapping. Each replacement value may itself contain one or more such placeholders. Each placeholder is replaced by the value associated
with its corresponding replacement key.

Return the fully substituted text string which does not contain any placeholders.

Example 1:

Input: replacements = [["C","%G%%J%z"],["G","n%R%an"],["J","q"],["R","sb%J%w"],["A","ug%C%ln"]], text = "%A%_%R%_%C%_%G%_%J%"

Output: "ugnsbqwanqzln_sbqw_nsbqwanqz_nsbqwan_q"
```

First we can create a dependency graph, which creates directed edges between the dependecy and dependent. A key to remember is to also go from less 
dependent to more dependent node. For example, in the above question, when we are creating an edge for key "C", we will create these edges:

```
G -> C
J -> C

and not C -> G; C -> J

This is done to make sure that we are visiting the less dependent nodes first and once visited we can move their dependent nodes, neighbours, after.

Say now we have a dependencyGraph: unordered_map<type, vector<type>>
```

Once the graph is created. We can either use Kahn's algorithm or a simple (and brief) dfs. 
Kahn's algorithm is more intuitive, while dfs one is more brief in implementation but uses recursion. Pick your poison accordingly.

## Kahn's Algorithm
```
Kahn's Algorithm works by repeatedly finding vertices with no incoming edges, removing them from the graph, and updating the incoming edges of the
vertices connected from the removed removed edges. This process continues until all vertices have been ordered.
```

### Implementation components:
1. Indegree for each node
2. Queue for processing

#### 1. Indegree for each node
This indicates the edges which are pointing to a node.

```
from the above example
G -> C
J -> C

indegree
C: 2
G: 0
J: 0
```
#### 2. Queue for processing - contains only zero indegree nodes
We are taking a BFS type of approach while processing the elements. Also, you will notice in the algorithm that elements which have ingress of 0 only they
reside in the queue at any given point.

### Algorithm
1. Add all nodes with in-degree 0 to a queue.
2. While the queue is not empty:
   Remove a node from the queue.
   For each outgoing edge from the removed node (neighbours),
     decrement the in-degree of the destination node by 1.
     If the in-degree of a destination node becomes 0, add it to the queue.
4. If the queue is empty and there are still nodes in the graph, the graph contains a cycle and cannot be topologically sorted.
5. The nodes in the queue represent the topological ordering of the graph.

#### Cycle detection
It detects cycle by saying that there are nodes in the graph, among which none has 0 in-degree. Which makes sense. So a simple check like this will work:

```
topSortOrdering.size() != numberOfNodesInGraph
```
