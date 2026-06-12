The core distinction comes down to what you're searching *for* and what the shape of the search space looks like.

## BFS — when you need "shortest" or "level by level"

**Shortest path in unweighted graphs.** BFS explores level by level, so the first time you reach a target node, you've reached it via the minimum number of edges. This is the single most common BFS trigger — "minimum number of steps/moves/jumps" almost always means BFS. (For weighted graphs, Dijkstra generalizes this idea, which is why it's structurally a "weighted BFS" with a priority queue instead of a plain queue.)

**Level-order processing.** Any problem that explicitly cares about "distance from source" or processes a tree/graph layer by layer — binary tree level-order traversal, "rotting oranges" style multi-source spread, finding all nodes within k steps.

**Finding the nearest match.** If you need the *closest* occurrence of something (nearest exit in a maze, nearest 0 in a binary matrix), BFS guarantees you find it first because of the level-by-level guarantee.

**Bidirectional search candidates.** When both the start and end states are known and the search space is huge (e.g., word ladder), BFS from both ends meeting in the middle cuts the exponential blowup roughly in half — DFS doesn't support this as cleanly.

## DFS — when you need to explore full paths or detect structure

**Enumeration / backtracking.** Any "generate all X" problem — permutations, combinations, subsets, all paths from A to B — is DFS, because you need to go deep along one path before exploring alternatives, and you naturally backtrack to try the next branch. This is basically your entire backtracking prep.

**Connectivity and component detection.** Counting connected components, checking if a graph is bipartite, detecting cycles — DFS's recursive structure makes it natural to track visited state and parent relationships as you go.

**Topological sort.** DFS with post-order finishing times gives you a topological ordering directly (the "finish, then prepend" pattern) — this is the classic DFS application for DAGs.

**When the answer requires going "all the way down" first.** Things like "max depth of tree," "is this a valid path that satisfies constraint X along the way," or tree-shape problems where you need information from leaves before you can decide something at the root.

## The deeper "why"

The real distinguishing factor is the data structure driving the traversal: BFS uses a **queue** (FIFO — process in the order discovered, which enforces level-by-level expansion), DFS uses a **stack** (LIFO — implicit via recursion, which lets you commit to a path and unwind). That single difference cascades into everything above: queue-based exploration naturally gives you "distance from source" as a byproduct, while stack-based exploration naturally gives you "full paths" and lets you maintain path-dependent state (like the `cols`/`diagonals` sets in your N-Queens code).

**Space tradeoff worth knowing:** BFS space is O(width of the graph) — for a tree that doubles each level, this can be O(2^(d-1)), exponential in depth. DFS space is O(depth) — the recursion stack only ever holds one path's worth of nodes. So for very wide, shallow graphs, DFS can be dramatically more memory-efficient even if BFS would find the answer "first" — this tradeoff sometimes comes up as a follow-up in interviews ("BFS finds it faster, but at what memory cost?").

A quick gut-check question for any new problem: *"Do I care about the minimum number of steps, or do I need to fully explore a candidate before deciding it's valid?"* — first answer points to BFS, second to DFS/backtracking.
