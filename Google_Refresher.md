## Written by Clause Sonnet 4.6

# 📘 Google L5 Interview — DSA Algorithm Mastersheet
> **C++ | 12 Categories | 25+ Algorithms | LeetCode Patterns**  
> Each section: Algorithm Steps → Commented C++ Code → Complexity Analysis → LeetCode Applications

---

## 📋 Table of Contents

| # | Topic | Category |
|---|-------|----------|
| 01 | [Binary Search + Lower/Upper Bound](#01-binary-search) | Searching |
| 02 | [Sliding Window & Two Pointers](#02-sliding-window--two-pointers) | Array / String |
| 03 | [DFS (Recursive + Iterative) + BFS](#03-dfs--bfs) | Graph Traversal |
| 04 | [Topological Sort (Kahn's) + Cycle Detection](#04-topological-sort--cycle-detection) | Graph / DAG |
| 05 | [Union-Find / DSU](#05-union-find--dsu) | Connectivity |
| 06 | [Dijkstra's Algorithm](#06-dijkstras-algorithm) | Shortest Path |
| 07 | [Bellman-Ford + Floyd-Warshall](#07-bellman-ford--floyd-warshall) | Shortest Path |
| 08 | [Kruskal's Algorithm (MST)](#08-kruskals-algorithm--mst) | Graph / MST |
| 09 | [DP: Knapsack · LCS · Coin Change · LIS](#09-dynamic-programming) | Dynamic Programming |
| 10 | [LRU Cache Design](#10-lru-cache) | System Design |
| 11 | [Monotonic Stack ⭐](#11-monotonic-stack) | Advanced |
| 12 | [Trie (Prefix Tree) ⭐](#12-trie) | Advanced |

---

## 01. Binary Search

> **Core Idea:** On a sorted array, compare the mid element to the target. Eliminate the half that cannot contain the answer.  
> **Prerequisite:** Array must be sorted (or a monotone condition must exist).

### 📋 Algorithm Steps

1. **Init pointers** — `lo = 0`, `hi = n - 1` (inclusive boundaries)
2. **Compute mid safely** — `mid = lo + (hi - lo) / 2` — avoids integer overflow
3. **Compare** — if `arr[mid] == target` → return `mid`. If `arr[mid] < target` → `lo = mid + 1`. Else → `hi = mid - 1`
4. **Repeat** until `lo > hi`. Return `-1` if not found.

### 💡 Intuition
Every iteration the search space halves: `n → n/2 → n/4 → ... → 1`. After `log₂(n)` steps the space is exhausted. This is why binary search is `O(log n)`.

### 💻 C++ Code

```cpp
// Binary Search — returns index of target, or -1 if not found
// Precondition: arr is sorted ascending
int binarySearch(vector<int>& arr, int target) {
    int lo = 0, hi = (int)arr.size() - 1;

    while (lo <= hi) {
        // Safe mid: prevents (lo + hi) overflow for large indices
        int mid = lo + (hi - lo) / 2;

        if      (arr[mid] == target) return mid;       // Found
        else if (arr[mid] <  target) lo = mid + 1;     // Target in right half
        else                         hi = mid - 1;     // Target in left half
    }
    return -1; // Not found
}

// ── Lower Bound ──────────────────────────────────────────────────────────────
// First index where arr[i] >= target  (like std::lower_bound)
int lowerBound(vector<int>& arr, int target) {
    int lo = 0, hi = (int)arr.size(); // hi = n (open right boundary)
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (arr[mid] < target) lo = mid + 1;
        else                   hi = mid;    // mid could be the answer; keep it
    }
    return lo; // lo == hi == answer
}

// ── Upper Bound ──────────────────────────────────────────────────────────────
// First index where arr[i] > target  (like std::upper_bound)
int upperBound(vector<int>& arr, int target) {
    int lo = 0, hi = (int)arr.size();
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (arr[mid] <= target) lo = mid + 1;
        else                    hi = mid;
    }
    return lo;
}

// ── STL equivalents (prefer these in production) ─────────────────────────────
// auto it = lower_bound(arr.begin(), arr.end(), target);
// auto it = upper_bound(arr.begin(), arr.end(), target);
// Distance from begin = index: it - arr.begin()
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time | `O(log n)` | Search space halves each step: n → n/2 → … → 1 = log₂(n) iterations |
| Space | `O(1)` | Only `lo`, `hi`, `mid` — no extra structures |

### 🔑 Key Template Insight

> **If you can validate a condition in O(n), wrapping it in Binary Search gives O(n log n).**

| Boundary Style | Use `lo = 0, hi = n-1` | Use `lo = 0, hi = n` |
|---|---|---|
| When | Exact value search | Lower/Upper bound style |
| Loop | `while (lo <= hi)` | `while (lo < hi)` |
| Returns | Index or -1 | Insertion point |

### 🟢 LeetCode Applications

| Problem | Why this algorithm |
|---------|-------------------|
| LC 704 — Binary Search | Direct application on sorted array |
| LC 34 — First & Last Position | Two binary searches: lower_bound + upper_bound |
| LC 33 — Search in Rotated Array | BS with pivot detection — check which half is sorted |
| LC 153 — Min in Rotated Array | BS on monotone condition: `arr[mid] > arr[hi]` |
| LC 875 — Koko Eating Bananas | BS on **answer space** (speed 1..max), not the array |
| LC 1011 — Capacity to Ship | BS on answer space (capacity) — classic "BS on answer" template |

---

## 02. Sliding Window & Two Pointers

> **Core Idea:** Maintain a window `[left, right]` and expand/shrink it to satisfy a constraint. Avoids `O(n²)` nested loops.  
> **When to use:** Subarray/substring problems with a monotone property — if window is valid, extending may keep it valid; if invalid, shrinking may fix it.

### 📋 Algorithm Steps (Variable-size window)

1. **Init** — `left = 0`, `right = 0`, state variable (freq map / sum / count)
2. **Expand right** — add `arr[right]` to window state, increment `right`
3. **Shrink left if invalid** — while window violates constraint: remove `arr[left]`, `left++`
4. **Record answer** — after shrinking, window is maximally valid: `result = max(result, right - left)`
5. **Repeat** until `right` reaches `n`

### 💻 C++ Code

```cpp
// ── Pattern 1: Longest subarray with at most K distinct elements ─────────────
int longestKDistinct(vector<int>& arr, int k) {
    unordered_map<int,int> freq; // element → count in window
    int left = 0, result = 0;

    for (int right = 0; right < (int)arr.size(); right++) {
        freq[arr[right]]++;              // expand: add right element

        // Shrink window until at most k distinct elements remain
        while ((int)freq.size() > k) {
            freq[arr[left]]--;
            if (freq[arr[left]] == 0) freq.erase(arr[left]);
            left++;
        }
        result = max(result, right - left + 1);
    }
    return result;
}

// ── Pattern 2: Fixed-size window — max sum of k elements ─────────────────────
int maxSumFixedWindow(vector<int>& arr, int k) {
    int windowSum = 0;
    for (int i = 0; i < k; i++) windowSum += arr[i]; // seed first window

    int maxSum = windowSum;
    for (int right = k; right < (int)arr.size(); right++) {
        // Slide: add new element on right, drop leftmost element
        windowSum += arr[right] - arr[right - k];
        maxSum = max(maxSum, windowSum);
    }
    return maxSum;
}

// ── Pattern 3: Two Pointers — pair sum equals target (sorted array) ───────────
vector<int> twoSumSorted(vector<int>& arr, int target) {
    int lo = 0, hi = (int)arr.size() - 1;
    while (lo < hi) {
        int sum = arr[lo] + arr[hi];
        if      (sum == target) return {lo, hi};
        else if (sum  < target) lo++;  // need a larger value
        else                    hi--;  // need a smaller value
    }
    return {}; // no pair found
}
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time | `O(n)` | Each element enters AND leaves the window exactly once → 2n total ops |
| Space | `O(k)` or `O(1)` | `O(k)` for freq map (k distinct elements); `O(1)` for sum-based windows |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 3 — Longest Substring No Repeat | Variable window + char freq map |
| LC 76 — Minimum Window Substring | Variable window with "required chars" count |
| LC 438 — Find All Anagrams | Fixed window of size `p`, compare freq maps |
| LC 424 — Longest Repeating Char Replacement | Window valid if `(len - maxFreq) <= k` |
| LC 239 — Sliding Window Maximum | Fixed window + monotonic deque for O(1) max |
| LC 167 — Two Sum II | Two pointers on sorted array |

---

## 03. DFS & BFS

> **DFS:** Explores as deep as possible before backtracking. Uses recursion stack or explicit stack.  
> **BFS:** Explores level by level. Uses a queue. **Guarantees shortest path on unweighted graphs.**

### 📋 DFS Steps

1. **Mark visited** — prevents infinite loops in cyclic graphs
2. **Process node** — record, check goal, etc.
3. **Recurse on unvisited neighbours** — for each `v` in `adj[u]`: if not visited → `dfs(v)`
4. **Backtrack** — implicit on return (recursive) or `stk.pop()` (iterative)

### 📋 BFS Steps

1. **Init** — `dist[src] = 0`, push `src` to queue. All others `dist = -1`.
2. **Dequeue node `u`** — process it
3. **Enqueue unvisited neighbours** — for each `v` in `adj[u]`: if `dist[v] == -1` → set `dist[v] = dist[u] + 1`, enqueue
4. **Repeat** until queue empty

### 💻 C++ Code

```cpp
#include <bits/stdc++.h>
using namespace std;
// Graph: adjacency list. n nodes, 0-indexed.

// ── DFS Recursive ─────────────────────────────────────────────────────────────
void dfsRecursive(int node, vector<vector<int>>& adj, vector<bool>& vis) {
    vis[node] = true;
    cout << node << " ";  // process node here

    for (int nb : adj[node])
        if (!vis[nb])
            dfsRecursive(nb, adj, vis);
}

// ── DFS Iterative (explicit stack — avoids stack overflow on deep graphs) ──────
void dfsIterative(int start, vector<vector<int>>& adj, int n) {
    vector<bool> vis(n, false);
    stack<int> stk;

    // Mark when PUSHING to avoid processing a node multiple times
    vis[start] = true;
    stk.push(start);

    while (!stk.empty()) {
        int node = stk.top(); stk.pop();
        cout << node << " ";

        // Push in reverse to match recursive traversal order
        for (int i = (int)adj[node].size() - 1; i >= 0; i--) {
            int nb = adj[node][i];
            if (!vis[nb]) {
                vis[nb] = true;
                stk.push(nb);
            }
        }
    }
}

// ── BFS — also finds shortest path on unweighted graph ────────────────────────
vector<int> bfs(int start, vector<vector<int>>& adj, int n) {
    vector<int> dist(n, -1);  // -1 = unvisited
    queue<int> q;

    dist[start] = 0;
    q.push(start);

    while (!q.empty()) {
        int node = q.front(); q.pop();

        for (int nb : adj[node]) {
            if (dist[nb] == -1) {         // not yet visited
                dist[nb] = dist[node] + 1;
                q.push(nb);
            }
        }
    }
    return dist; // dist[i] = shortest hop-count from start to i
}

// ── Multi-source BFS ──────────────────────────────────────────────────────────
// Push ALL sources at distance 0 before starting the loop.
// Propagates minimum distance from the nearest source naturally.
// Example use: LC 994 Rotting Oranges — all rotten cells start at dist 0.
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time (DFS & BFS) | `O(V + E)` | Each vertex visited once (V). Each edge examined once (E). |
| Space DFS | `O(V)` | Recursion stack depth = O(V) worst case (linear chain graph) |
| Space BFS | `O(V)` | Queue holds at most one full level, worst case O(V) |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 200 — Number of Islands | DFS/BFS flood-fill to count connected components |
| LC 994 — Rotting Oranges | Multi-source BFS from all rotten cells simultaneously |
| LC 127 — Word Ladder | BFS on word graph; each edge = one letter change |
| LC 130 — Surrounded Regions | DFS from border O's; mark safe regions |
| LC 695 — Max Area of Island | DFS returning component size |
| LC 542 — 01 Matrix | Multi-source BFS from all 0-cells |

---

## 04. Topological Sort & Cycle Detection

> **Kahn's Algorithm:** BFS-based topo sort using in-degree array. Start from nodes with in-degree 0.  
> **Cycle Detection (Kahn's):** If topo sort visits fewer than V nodes → cycle exists.  
> **DFS Cycle Detection:** 3-colour marking: `WHITE` (unvisited), `GRAY` (in current DFS path), `BLACK` (fully done).

### 📋 Kahn's Algorithm Steps

1. **Compute in-degree** — for every edge `u→v`, increment `indegree[v]`
2. **Enqueue zero-indegree nodes** — all nodes with `indegree[u] == 0` are ready to process
3. **BFS** — dequeue `u`, append to `order`. For each neighbour `v`: `indegree[v]--`. If `indegree[v] == 0`, enqueue `v`
4. **Check** — `order.size() == V` → valid DAG. Otherwise → cycle exists

### 💻 C++ Code

```cpp
// ── Kahn's Algorithm — Topological Sort + Cycle Detection ─────────────────────
vector<int> kahnTopoSort(int V, vector<vector<int>>& adj) {
    vector<int> indegree(V, 0);

    // Count incoming edges for each node
    for (int u = 0; u < V; u++)
        for (int v : adj[u])
            indegree[v]++;

    queue<int> q;
    // Nodes with no prerequisites can be scheduled first
    for (int i = 0; i < V; i++)
        if (indegree[i] == 0) q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);

        for (int v : adj[u]) {
            indegree[v]--;             // "remove" edge u→v
            if (indegree[v] == 0)      // v is now a source node
                q.push(v);
        }
    }

    // Cycle check: if we couldn't process all nodes, a cycle prevented it
    if ((int)order.size() != V) return {}; // empty vector signals cycle
    return order;
}

// ── DFS-based Cycle Detection in Directed Graph ───────────────────────────────
// color: 0 = unvisited (WHITE), 1 = in current DFS path (GRAY), 2 = done (BLACK)
bool dfsCycle(int u, vector<vector<int>>& adj, vector<int>& color) {
    color[u] = 1; // Mark as in-progress (on current path)

    for (int v : adj[u]) {
        if (color[v] == 1) return true;   // Back edge → cycle found!
        if (color[v] == 0)                // Unvisited — explore
            if (dfsCycle(v, adj, color)) return true;
    }

    color[u] = 2; // Fully explored — no cycle through u
    return false;
}

bool hasCycle(int V, vector<vector<int>>& adj) {
    vector<int> color(V, 0);
    for (int i = 0; i < V; i++)
        if (color[i] == 0)
            if (dfsCycle(i, adj, color)) return true;
    return false;
}
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time (Kahn's) | `O(V + E)` | Each node enqueued/dequeued once. Each edge processed once. |
| Time (DFS Cycle) | `O(V + E)` | Standard DFS — visits every vertex and edge once |
| Space | `O(V + E)` | In-degree array O(V) + queue O(V) + adjacency list O(E) |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 207 — Course Schedule | Cycle detection in prerequisite graph |
| LC 210 — Course Schedule II | Return topological order using Kahn's |
| LC 269 — Alien Dictionary | Build graph from adjacent words, topo sort chars |
| LC 310 — Min Height Trees | Repeatedly remove leaf nodes (structurally like Kahn's) |
| LC 802 — Find Eventual Safe States | Reverse graph + topo sort / DFS 3-coloring |

---

## 05. Union-Find / DSU

> **Core Idea:** Track which elements belong to the same set. Two operations: `find` (which set?) and `union` (merge two sets).  
> **Path Compression:** During `find()`, point every node directly to root. Flattens tree for future queries.  
> **Union by Rank:** Always attach smaller tree under taller. Keeps tree height `O(log n)`.  
> **Combined:** Nearly `O(1)` per operation — amortized `O(α(n))` where α is the inverse Ackermann function (≤ 4 for all practical n).

### 📋 Algorithm Steps

1. **Init** — `parent[i] = i`, `rank[i] = 0`. Each node is its own set.
2. **find(x)** — if `parent[x] != x`: recursively find root AND compress path (`parent[x] = find(parent[x])`)
3. **union(x, y)** — find roots `rx = find(x)`, `ry = find(y)`. If same → already connected. Else attach by rank.
4. **connected(x, y)** — check `find(x) == find(y)`

### 💻 C++ Code

```cpp
struct DSU {
    vector<int> parent, rank_;

    // Initialize: every node is its own parent (n separate sets)
    DSU(int n) : parent(n), rank_(n, 0) {
        iota(parent.begin(), parent.end(), 0); // parent[i] = i
    }

    // Find root with PATH COMPRESSION
    // Recursively sets parent[x] = root, flattening the tree
    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]); // compress: point directly to root
        return parent[x];
    }

    // Union by RANK — attach shorter tree under taller
    // Returns true if they were in different sets (merge happened)
    bool unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx == ry) return false; // already in the same set

        // Attach smaller rank under larger rank
        if (rank_[rx] < rank_[ry]) swap(rx, ry);
        parent[ry] = rx;                           // ry goes under rx
        if (rank_[rx] == rank_[ry]) rank_[rx]++;   // equal ranks → increment
        return true;
    }

    bool connected(int x, int y) { return find(x) == find(y); }
};

// ── Usage: count connected components ─────────────────────────────────────────
int countComponents(int n, vector<vector<int>>& edges) {
    DSU dsu(n);
    int components = n; // start: n separate singleton sets

    for (auto& e : edges) {
        if (dsu.unite(e[0], e[1]))
            components--; // merged two components → one fewer
    }
    return components;
}
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| find / union | `O(α(n)) ≈ O(1)` | α is inverse Ackermann. For all practical n (< 10^600), α(n) ≤ 4 |
| Space | `O(n)` | Two arrays of size n: `parent[]` and `rank_[]` |
| Without optimizations | `O(n)` per op | Degenerate tree → linked list. Never acceptable. |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 547 — Number of Provinces | Count components via DSU |
| LC 684 — Redundant Connection | First edge where `unite()` returns false (already connected) |
| LC 1319 — Make Network Connected | Count redundant edges; check if enough to reconnect |
| LC 778 — Swim in Rising Water | Sort edges by weight, unite; stop when 0 connects to n²-1 |
| LC 1202 — Smallest String Swapping | Characters in same component can be freely rearranged |

---

## 06. Dijkstra's Algorithm

> **Core Idea:** Greedy BFS with a min-heap. Always process the globally unfinished node with smallest known distance first.  
> **Constraint:** Edge weights must be **NON-NEGATIVE**. Use Bellman-Ford if negative weights exist.

### 📋 Algorithm Steps

1. **Init** — `dist[src] = 0`, `dist[others] = INF`. Push `(0, src)` into min-heap.
2. **Extract minimum** — pop `(d, u)`. If `d > dist[u]`, skip (stale entry from lazy deletion).
3. **Relax neighbours** — for each edge `u→v` with weight `w`: if `dist[u] + w < dist[v]` → update `dist[v]`, push `(dist[v], v)`.
4. **Repeat** until heap empty. `dist[]` has shortest paths from `src`.

### 💻 C++ Code

```cpp
// Dijkstra — single source shortest paths (non-negative weights only)
// adj[u] = list of {neighbour, weight}
vector<long long> dijkstra(int src, int V,
                            vector<vector<pair<int,int>>>& adj) {
    const long long INF = 1e18;
    vector<long long> dist(V, INF);

    // min-heap: {distance, node}
    priority_queue<pair<long long,int>,
                   vector<pair<long long,int>>,
                   greater<>> pq;

    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();

        // Lazy deletion: skip outdated entries
        // (we may push multiple entries for the same node)
        if (d > dist[u]) continue;

        for (auto [v, w] : adj[u]) {
            // Relaxation: can we reach v faster through u?
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist; // dist[i] = shortest distance src → i, INF if unreachable
}

// To reconstruct the actual path, maintain a prev[] array:
// In relaxation block: prev[v] = u;
// Then trace back: v → prev[v] → prev[prev[v]] → ... → src
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time | `O((V + E) log V)` | Each vertex extracted once: O(V log V). Each edge may push to heap: O(E log V). |
| Space | `O(V + E)` | `dist[]` array O(V). Heap holds O(E) entries worst case. |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 743 — Network Delay Time | Classic Dijkstra from node k; answer = `max(dist)` |
| LC 1631 — Min Effort Path | Dijkstra on grid; edge weight = `|height diff|` |
| LC 787 — Cheapest Flights K Stops | Modified Dijkstra with stop count as state dimension |
| LC 1514 — Path with Max Probability | Dijkstra with max-heap, multiply probabilities |
| LC 505 — The Maze II | Dijkstra on grid, state = (row, col) |

---

## 07. Bellman-Ford + Floyd-Warshall

> **Bellman-Ford:** Single-source. Handles **negative weights**. Detects negative cycles. Relax all edges V-1 times.  
> **Floyd-Warshall:** **All-pairs** shortest paths. `O(V³)`. Works with negative weights (not negative cycles). DP on intermediate nodes.

### 📋 Bellman-Ford Steps

1. **Init** — `dist[src] = 0`, `dist[others] = INF`
2. **Relax V-1 times** — for every edge `(u, v, w)`: if `dist[u] + w < dist[v]` → update
3. **Detect negative cycle** — one more pass; if any update happens → negative cycle reachable from src

### 📋 Floyd-Warshall Steps

1. **Init dp[i][j]** — weight of edge `i→j` if exists, `0` if `i == j`, `INF` otherwise
2. **For k = 0..V-1** (intermediate node): for all `i, j`: `dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j])`
3. **Check diagonal** — if `dp[i][i] < 0` → negative cycle

### 💻 C++ Code

```cpp
// ── Bellman-Ford ──────────────────────────────────────────────────────────────
// edges: list of {u, v, weight}
vector<long long> bellmanFord(int src, int V,
                               vector<tuple<int,int,int>>& edges) {
    const long long INF = 1e18;
    vector<long long> dist(V, INF);
    dist[src] = 0;

    // Relax all edges V-1 times
    // After k iterations, dist[v] = shortest path using at most k edges
    for (int iter = 0; iter < V - 1; iter++) {
        for (auto& [u, v, w] : edges) {
            if (dist[u] != INF && dist[u] + w < dist[v])
                dist[v] = dist[u] + w;
        }
    }

    // V-th relaxation: if anything still updates → negative cycle
    for (auto& [u, v, w] : edges) {
        if (dist[u] != INF && dist[u] + w < dist[v])
            return {}; // empty = negative cycle detected
    }
    return dist;
}

// ── Floyd-Warshall — All-Pairs Shortest Paths ─────────────────────────────────
vector<vector<long long>> floydWarshall(int V,
    vector<vector<pair<int,int>>>& adj) {

    const long long INF = 1e18;
    // Initialize: direct edge weights, 0 on diagonal, INF otherwise
    vector<vector<long long>> dp(V, vector<long long>(V, INF));
    for (int i = 0; i < V; i++) dp[i][i] = 0;
    for (int u = 0; u < V; u++)
        for (auto [v, w] : adj[u])
            dp[u][v] = min(dp[u][v], (long long)w);

    // Try every node k as an intermediate relay
    for (int k = 0; k < V; k++)
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                if (dp[i][k] != INF && dp[k][j] != INF)
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j]);

    return dp;
}
```

### 📊 Complexity Analysis

| Algorithm | Time | Space | Notes |
|-----------|------|-------|-------|
| Bellman-Ford | `O(V × E)` | `O(V)` | V-1 passes × E edges each |
| Floyd-Warshall | `O(V³)` | `O(V²)` | Triple nested loop over all pairs + intermediate |

### When to choose which?

| Situation | Algorithm |
|-----------|-----------|
| Non-negative weights, single source | **Dijkstra** |
| Negative weights, single source | **Bellman-Ford** |
| Need to detect negative cycles | **Bellman-Ford** |
| All-pairs, small V (≤ 500) | **Floyd-Warshall** |
| K-stop constraint | **Bellman-Ford** (natural K iterations) |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 787 — Cheapest Flights K Stops | Bellman-Ford with exactly K iterations limits stops naturally |
| LC 1334 — Find City Within Threshold | Floyd-Warshall for all-pairs, count reachable within threshold |
| LC 399 — Evaluate Division | Build weighted graph, Floyd-Warshall for all query pairs |

---

## 08. Kruskal's Algorithm — MST

> **Core Idea:** Sort all edges by weight. Greedily add the cheapest edge that doesn't form a cycle (use DSU for cycle detection).  
> **Result:** MST — V-1 edges connecting all vertices with minimum total weight.

### 📋 Algorithm Steps

1. **Sort edges** — by weight ascending
2. **Init DSU** — each vertex in its own component
3. **Iterate edges** — for edge `(u, v, w)`: if `find(u) != find(v)` → add to MST, `unite(u, v)`. Else skip (would form cycle).
4. **Stop** — when MST has `V-1` edges (or all edges exhausted)

### 💻 C++ Code

```cpp
struct Edge { int u, v, w; };

// Kruskal's MST — uses DSU from section 05
long long kruskal(int V, vector<Edge>& edges) {
    // Greedy choice: always pick cheapest safe edge
    sort(edges.begin(), edges.end(),
         [](const Edge& a, const Edge& b) { return a.w < b.w; });

    DSU dsu(V); // See DSU section
    long long mstCost = 0;
    int edgesUsed = 0;

    for (auto& [u, v, w] : edges) {
        if (dsu.unite(u, v)) {   // true = different components → safe to add
            mstCost += w;
            edgesUsed++;
            if (edgesUsed == V - 1) break; // MST complete — V-1 edges
        }
        // else: u and v already connected → adding this edge makes a cycle
    }

    // If edgesUsed < V-1, graph is disconnected (no spanning tree exists)
    return (edgesUsed == V - 1) ? mstCost : -1;
}
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time | `O(E log E)` | Sorting edges dominates. DSU operations are near O(1) each. |
| Space | `O(V + E)` | DSU arrays O(V) + sorted edge list O(E) |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 1584 — Min Cost Connect Points | MST on complete graph (Manhattan distance edges) |
| LC 778 — Swim in Rising Water | Sort edges, unite; stop when src and dst are connected |
| LC 1489 — Find Critical/Pseudo-Critical Edges | Run Kruskal with/without specific edges |

---

## 09. Dynamic Programming

> **Key Insight:** Break problem into overlapping subproblems. Store results to avoid recomputation.  
> **Identify DP:** Optimal substructure + overlapping subproblems. Choices at each step depend on previous states.

---

### 9A — 0/1 Knapsack

> **State:** `dp[w]` = max value achievable with capacity `w`  
> **Transition:** `dp[w] = max(dp[w], dp[w - weight[i]] + value[i])`  
> **Key trick:** Traverse **backwards** to prevent using item `i` twice (forward traversal = unbounded knapsack)

```cpp
// 0/1 Knapsack — O(n*W) time, O(W) space (1D optimisation)
int knapsack(vector<int>& wt, vector<int>& val, int W) {
    int n = wt.size();
    // dp[w] = max value achievable with exactly capacity w available
    vector<int> dp(W + 1, 0);

    for (int i = 0; i < n; i++) {
        // CRITICAL: traverse backwards — prevents using item i more than once
        // Forward traversal would allow: dp[w] uses dp[w-wt[i]] which already
        // includes item i → equivalent to unbounded knapsack
        for (int w = W; w >= wt[i]; w--) {
            dp[w] = max(dp[w],              // skip item i
                        dp[w - wt[i]] + val[i]); // take item i
        }
    }
    return dp[W];
}
```

**Complexity:** Time `O(n × W)` | Space `O(W)`

---

### 9B — Longest Common Subsequence (LCS)

> **State:** `dp[i][j]` = LCS length of `s1[0..i-1]` and `s2[0..j-1]`  
> **Transition:**  
> - If `s1[i-1] == s2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1`  
> - Else: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`

```cpp
// LCS — O(m*n) time and space
int lcs(string& s1, string& s2) {
    int m = s1.size(), n = s2.size();
    // dp[i][j] = LCS of first i chars of s1, first j chars of s2
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i-1] == s2[j-1])
                dp[i][j] = dp[i-1][j-1] + 1;           // chars match
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]); // skip one char
        }
    }
    return dp[m][n];
}
// Space optimisation: keep only 2 rows → O(n) space
```

**Complexity:** Time `O(m × n)` | Space `O(m × n)` (reducible to `O(n)`)

---

### 9C — Coin Change (Minimum Coins)

> **State:** `dp[i]` = minimum coins needed to make amount `i`  
> **Transition:** `dp[i] = min over all coins c: dp[i - c] + 1`

```cpp
// Coin Change — minimum number of coins to reach target amount
int coinChange(vector<int>& coins, int amount) {
    // Use amount+1 as "infinity" (impossible to need more coins than amount)
    vector<int> dp(amount + 1, amount + 1);
    dp[0] = 0; // base case: 0 coins needed to make amount 0

    for (int i = 1; i <= amount; i++) {
        for (int c : coins) {
            if (c <= i) {
                // Option: use coin c, then solve for (i - c)
                dp[i] = min(dp[i], dp[i - c] + 1);
            }
        }
    }
    // If still "infinity", amount cannot be reached
    return dp[amount] > amount ? -1 : dp[amount];
}
```

**Complexity:** Time `O(amount × |coins|)` | Space `O(amount)`

---

### 9D — Longest Increasing Subsequence (LIS) — O(n log n)

> **Key:** Maintain `tails[]` where `tails[i]` = smallest tail element of all IS of length `i+1`.  
> **Property:** `tails[]` is always sorted → binary search applies.  
> Length of `tails` = LIS length.

```cpp
// LIS in O(n log n) — patience sorting idea
int lis(vector<int>& nums) {
    vector<int> tails; // tails[i] = smallest tail of any IS of length i+1

    for (int num : nums) {
        // Binary search: find first tail element >= num
        auto it = lower_bound(tails.begin(), tails.end(), num);

        if (it == tails.end()) {
            tails.push_back(num); // num extends the longest IS found so far
        } else {
            *it = num; // Replace: smaller tail = more room for future elements
        }
        // NOTE: tails itself is NOT the actual LIS, only its size is correct
    }
    return (int)tails.size();
}
// For non-decreasing LIS (allows equals): use upper_bound instead
// For strictly decreasing LIS: negate all elements + lower_bound
```

**Complexity:** Time `O(n log n)` | Space `O(n)`

---

### 🟢 DP LeetCode Applications

| Problem | DP Type |
|---------|---------|
| LC 416 — Partition Equal Subset Sum | 0/1 Knapsack — reach sum = total/2? |
| LC 494 — Target Sum | 0/1 Knapsack with +/- choices |
| LC 1143 — LCS | Classic LCS |
| LC 1092 — Shortest Common Supersequence | Build supersequence from LCS |
| LC 72 — Edit Distance | LCS variant with insert/delete/replace costs |
| LC 322 — Coin Change | Classic unbounded knapsack |
| LC 518 — Coin Change 2 (count ways) | Unbounded knapsack, count combinations |
| LC 300 — LIS | Classic O(n log n) LIS |
| LC 354 — Russian Doll Envelopes | Sort + LIS on 2D (sort by w asc, h desc) |

---

## 10. LRU Cache

> **Core Idea:** HashMap for `O(1)` lookup. Doubly linked list for `O(1)` move-to-front and eviction.  
> **Invariant:** Most recently used = head of list. Least recently used = tail. Evict tail when capacity exceeded.

### 📋 Algorithm Steps

1. **get(key)** — if key in map: move node to head (MRU), return value. Else return -1.
2. **put(key, val)** — if key exists: update value, move to head. Else: create node at head. If `size > capacity`: evict tail node + remove from map.
3. **moveToHead(node)** — remove from current position (rewire `prev/next`). Insert after dummy head.

### 💻 C++ Code

```cpp
class LRUCache {
    struct Node {
        int key, val;
        Node *prev, *next;
        Node(int k, int v) : key(k), val(v), prev(nullptr), next(nullptr) {}
    };

    int capacity;
    unordered_map<int, Node*> cache; // key → node pointer for O(1) lookup
    Node *head, *tail;               // dummy sentinel nodes (simplify edge cases)

    // Remove node from its current position in the list
    void remove(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    // Insert node right after dummy head = mark as most recently used
    void insertFront(Node* node) {
        node->next = head->next;
        node->prev = head;
        head->next->prev = node;
        head->next = node;
    }

public:
    LRUCache(int cap) : capacity(cap) {
        // Dummy sentinels: head ↔ [real nodes] ↔ tail
        // Eliminates null checks at list boundaries
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head->next = tail;
        tail->prev = head;
    }

    int get(int key) {
        if (!cache.count(key)) return -1;
        Node* node = cache[key];
        remove(node);      // detach from current position
        insertFront(node); // move to most-recent (front)
        return node->val;
    }

    void put(int key, int value) {
        if (cache.count(key)) {
            // Update existing: refresh value and move to front
            cache[key]->val = value;
            remove(cache[key]);
            insertFront(cache[key]);
        } else {
            if ((int)cache.size() == capacity) {
                // Evict LRU: the real node just before dummy tail
                Node* lru = tail->prev;
                remove(lru);
                cache.erase(lru->key);
                delete lru;
            }
            Node* newNode = new Node(key, value);
            insertFront(newNode);
            cache[key] = newNode;
        }
    }
};
```

### 📊 Complexity Analysis

| Operation | Time | Reason |
|-----------|------|--------|
| `get` | `O(1)` | HashMap lookup + DLL pointer rewiring |
| `put` | `O(1)` | HashMap insert + DLL insert/delete with direct pointer |
| Space | `O(capacity)` | At most `capacity` nodes in list + map |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 146 — LRU Cache | Direct implementation |
| LC 460 — LFU Cache | Extension: evict least *frequently* used (freq → DLL map) |
| LC 432 — All O'one Data Structure | Min/Max + O(1) insert/delete — same DLL+map pattern |

---

## 11. Monotonic Stack

> **Core Idea:** Maintain a stack where elements are always in sorted order (increasing or decreasing). Pop when invariant breaks — popped elements have found their "answer" (NGE, NSE, etc.).  
> **Pattern recognition:** "Next/Previous Greater/Smaller Element" → monotonic stack.

### 📋 Algorithm Steps (Next Greater Element)

1. Iterate left to right. For each element `arr[i]`:
2. **Pop** all stack elements smaller than `arr[i]` — `arr[i]` is their Next Greater Element.
3. **Push** index `i` onto stack.
4. After iteration, remaining elements in stack have no NGE → answer `-1`.

### 💻 C++ Code

```cpp
// ── Next Greater Element — O(n) ───────────────────────────────────────────────
vector<int> nextGreater(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> stk; // stores indices; maintains decreasing values in stack

    for (int i = 0; i < n; i++) {
        // All elements in stack smaller than arr[i] found their NGE
        while (!stk.empty() && arr[stk.top()] < arr[i]) {
            result[stk.top()] = arr[i];
            stk.pop();
        }
        stk.push(i);
    }
    // Elements remaining in stack: no NGE exists → result stays -1
    return result;
}

// ── Largest Rectangle in Histogram — O(n) ────────────────────────────────────
int largestRectangle(vector<int>& heights) {
    stack<int> stk; // monotone increasing stack of bar indices
    int maxArea = 0;
    heights.push_back(0); // sentinel bar of height 0 flushes remaining stack

    for (int i = 0; i < (int)heights.size(); i++) {
        // When current bar is shorter, pop taller bars and calculate their span
        while (!stk.empty() && heights[stk.top()] > heights[i]) {
            int h = heights[stk.top()]; stk.pop();
            // Width extends from current position back to the bar below in stack
            int w = stk.empty() ? i : i - stk.top() - 1;
            maxArea = max(maxArea, h * w);
        }
        stk.push(i);
    }
    return maxArea;
}
```

### 📊 Complexity Analysis

| Metric | Value | Derivation |
|--------|-------|------------|
| Time | `O(n)` | Each element pushed once and popped at most once → 2n total ops |
| Space | `O(n)` | Stack holds at most n elements |

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 739 — Daily Temperatures | Next greater element (next warmer day) |
| LC 84 — Largest Rectangle in Histogram | Monotonic stack for span calculation |
| LC 85 — Maximal Rectangle | LC 84 applied row by row on a 2D grid |
| LC 42 — Trapping Rain Water | Two monotone stacks or two-pointer |
| LC 901 — Online Stock Span | Previous greater or equal element |
| LC 503 — Next Greater Element II (circular) | Traverse array twice (2n) |

---

## 12. Trie

> **Core Idea:** Tree where each edge represents a character. Path from root to a node = prefix. Supports `O(L)` insert/search where `L` = string length.  
> **Variants:** Binary Trie (XOR problems — each bit is an edge), Compressed Trie.

### 📋 Algorithm Steps

1. **Insert(word)** — walk from root; at each character, create child node if missing; mark `isEnd = true` at last node.
2. **Search(word)** — walk from root following characters; return `node->isEnd` at last node (false if path breaks).
3. **StartsWith(prefix)** — same as search but return `true` at end regardless of `isEnd`.

### 💻 C++ Code

```cpp
struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() : isEnd(false) {
        fill(children, children + 26, nullptr);
    }
};

struct Trie {
    TrieNode* root;
    Trie() : root(new TrieNode()) {}

    void insert(const string& word) {
        TrieNode* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx])
                cur->children[idx] = new TrieNode(); // create node if missing
            cur = cur->children[idx];
        }
        cur->isEnd = true; // mark end of valid word
    }

    bool search(const string& word) {
        TrieNode* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx]) return false; // path broken
            cur = cur->children[idx];
        }
        return cur->isEnd; // true only if complete word was inserted
    }

    bool startsWith(const string& prefix) {
        TrieNode* cur = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!cur->children[idx]) return false;
            cur = cur->children[idx];
        }
        return true; // prefix exists regardless of isEnd
    }
};

// ── Binary Trie for Max XOR ───────────────────────────────────────────────────
// Each number stored bit by bit (MSB to LSB).
// For each new number, greedily pick opposite bit to maximize XOR.
// LC 421 — Maximum XOR of Two Numbers in an Array
```

### 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Insert / Search | `O(L)` | L = length of word |
| Total space | `O(N × L × 26)` | N words, L chars each, 26-child pointer per node |

> **Optimisation:** Use `unordered_map<char, TrieNode*>` instead of fixed array for sparse alphabets.

### 🟢 LeetCode Applications

| Problem | Pattern |
|---------|---------|
| LC 208 — Implement Trie | Direct implementation |
| LC 211 — Word Dictionary (wildcard `.`) | DFS on trie; `.` matches all 26 children |
| LC 212 — Word Search II | Trie + DFS on board — prune branches not in trie |
| LC 421 — Max XOR of Two Numbers | Binary Trie; greedily pick opposite bit |
| LC 648 — Replace Words | Find shortest prefix in trie |
| LC 720 — Longest Word in Dictionary | Trie BFS/DFS with isEnd check at each level |

---

## 🗺️ Quick Reference: When to Use What

| Problem Signal | Algorithm |
|----------------|-----------|
| Sorted array, find value/position | **Binary Search** |
| Longest/shortest subarray with constraint | **Sliding Window** |
| Graph traversal, connected components | **DFS / BFS** |
| Shortest path, unweighted graph | **BFS** |
| Shortest path, non-negative weights | **Dijkstra** |
| Shortest path, negative weights | **Bellman-Ford** |
| Shortest path, all pairs | **Floyd-Warshall** |
| Schedule tasks with dependencies | **Topological Sort (Kahn's)** |
| Detect cycle in directed graph | **DFS 3-coloring or Kahn's** |
| Union/merge sets, connectivity | **DSU** |
| Minimum cost to connect all nodes | **Kruskal's MST** |
| Optimal substructure + overlapping subproblems | **Dynamic Programming** |
| O(1) get/put with eviction policy | **LRU Cache** |
| Next/Previous greater/smaller element | **Monotonic Stack** |
| Prefix search, word existence | **Trie** |
| Max XOR of pairs | **Binary Trie** |

---

*Last updated: Google L5 Prep — C++17 | All code tested conceptually against standard competitive programming patterns.*
