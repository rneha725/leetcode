# Google L5 — pattern decision guide with C++ examples

Each section: when to use it, why, an example problem, and a C++ snippet illustrating the pattern.

---

## 1. Greedy vs DP

**Test:** does a locally optimal choice ever foreclose a better global option? If you can prove (exchange argument) that it never does, greedy works. If "what if I'd chosen differently earlier" matters, you need DP.

**Greedy — Jump Game (LC 55)**
```cpp
bool CanJump(const vector<int>& nums) {
  int reach = 0;
  for (int i = 0; i < static_cast<int>(nums.size()); ++i) {
    if (i > reach) return false;
    reach = max(reach, i + nums[i]);
  }
  return true;
}
```

**DP — Coin Change (LC 322)**
```cpp
int CoinChange(const vector<int>& coins, int amount) {
  vector<int> dp(amount + 1, INT_MAX);
  dp[0] = 0;
  for (int a = 1; a <= amount; ++a) {
    for (int c : coins) {
      if (c <= a && dp[a - c] != INT_MAX) {
        dp[a] = min(dp[a], dp[a - c] + 1);
      }
    }
  }
  return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```

---

## 2. Memoization (top-down) vs tabulation (bottom-up)

**Use top-down** when the reachable state space is sparse (not all states matter).
**Use bottom-up** when n is large — avoids recursion depth limits and makes O(1) space rolling easy.

**Top-down — Climbing Stairs**
```cpp
int Climb(int n, vector<int>& memo) {
  if (n <= 1) return 1;
  if (memo[n] != -1) return memo[n];
  return memo[n] = Climb(n - 1, memo) + Climb(n - 2, memo);
}
```

**Bottom-up, space-optimized**
```cpp
int ClimbStairs(int n) {
  int prev2 = 1, prev1 = 1;
  for (int i = 2; i <= n; ++i) {
    int curr = prev1 + prev2;
    prev2 = prev1;
    prev1 = curr;
  }
  return prev1;
}
```

---

## 3. Two pointers vs sliding window vs prefix sums

**Two pointers** — sorted array, searching for a pair/triplet (LC 1, opposite-ends version).
```cpp
vector<int> TwoSumSorted(const vector<int>& nums, int target) {
  int lo = 0, hi = static_cast<int>(nums.size()) - 1;
  while (lo < hi) {
    int sum = nums[lo] + nums[hi];
    if (sum == target) return {lo, hi};
    if (sum < target) ++lo;
    else --hi;
  }
  return {};
}
```

**Sliding window** — contiguous subarray/substring with a running constraint (LC 340).
```cpp
int LengthOfLongestSubstringKDistinct(const string& s, int k) {
  unordered_map<char, int> freq;
  int left = 0, best = 0;
  for (int right = 0; right < static_cast<int>(s.size()); ++right) {
    freq[s[right]]++;
    while (static_cast<int>(freq.size()) > k) {
      freq[s[left]]--;
      if (freq[s[left]] == 0) freq.erase(s[left]);
      ++left;
    }
    best = max(best, right - left + 1);
  }
  return best;
}
```

**Prefix sums + hashmap** — non-contiguous validity, or "difference equals target" (LC 560).
```cpp
int SubarraySum(const vector<int>& nums, int k) {
  unordered_map<int, int> prefix_count{{0, 1}};
  int sum = 0, count = 0;
  for (int num : nums) {
    sum += num;
    count += prefix_count[sum - k];
    prefix_count[sum]++;
  }
  return count;
}
```

---

## 4. Union-Find vs DFS

**Union-Find** wins for: streaming edges + connectivity queries, Kruskal's MST, "are these already connected" checks without rebuilding state.
**DFS** wins for: directed graph cycle detection, when you need the actual path, or single-shot connectivity on a static graph.

```cpp
class UnionFind {
 public:
  explicit UnionFind(int n) : parent_(n), rank_(n, 0) {
    for (int i = 0; i < n; ++i) parent_[i] = i;
  }

  int Find(int x) {
    if (parent_[x] != x) parent_[x] = Find(parent_[x]);
    return parent_[x];
  }

  void Union(int x, int y) {
    int rx = Find(x), ry = Find(y);
    if (rx == ry) return;
    if (rank_[rx] < rank_[ry]) swap(rx, ry);
    parent_[ry] = rx;
    if (rank_[rx] == rank_[ry]) rank_[rx]++;
  }

 private:
  vector<int> parent_;
  vector<int> rank_;
};
```

```cpp
// DFS connectivity / component marking
void Dfs(int node, const vector<vector<int>>& adj, vector<bool>* visited) {
  (*visited)[node] = true;
  for (int nbr : adj[node]) {
    if (!(*visited)[nbr]) Dfs(nbr, adj, visited);
  }
}
```

---

## 5. Binary search on the answer

**Signal:** "minimum/maximum value such that condition X holds," and X is monotonic in that value. Turns a combinatorial search into O(log(range) * feasibility_cost).

**Capacity to Ship Packages Within D Days (LC 1011)**
```cpp
bool CanShip(const vector<int>& weights, int capacity, int days) {
  int needed_days = 1, curr_load = 0;
  for (int w : weights) {
    if (curr_load + w > capacity) {
      ++needed_days;
      curr_load = 0;
    }
    curr_load += w;
  }
  return needed_days <= days;
}

int ShipWithinDays(const vector<int>& weights, int days) {
  int lo = *max_element(weights.begin(), weights.end());
  int hi = accumulate(weights.begin(), weights.end(), 0);
  while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (CanShip(weights, mid, days)) hi = mid;
    else lo = mid + 1;
  }
  return lo;
}
```

---

## 6. Heap vs sorting

**Sort** when you need the full order, once. **Heap** when you need top-k (O(n log k) vs O(n log n)), or the set changes over time and you repeatedly need the extremum.

**Top-K Frequent Elements (LC 347)**
```cpp
vector<int> TopKFrequent(const vector<int>& nums, int k) {
  unordered_map<int, int> freq;
  for (int n : nums) freq[n]++;

  priority_queue<pair<int, int>> pq;  // (count, value) max-heap
  for (const auto& [val, count] : freq) pq.push({count, val});

  vector<int> result;
  for (int i = 0; i < k; ++i) {
    result.push_back(pq.top().second);
    pq.pop();
  }
  return result;
}
```

---

## 7. Recursive vs iterative DFS

Recursive is cleaner but uses O(h) stack — a skewed tree with 10^5 nodes can overflow. Iterative with an explicit stack avoids this; use when input size is unbounded.

```cpp
// Recursive
void DfsRecursive(TreeNode* node, vector<int>* result) {
  if (!node) return;
  result->push_back(node->val);
  DfsRecursive(node->left, result);
  DfsRecursive(node->right, result);
}
```

```cpp
// Iterative (preorder)
vector<int> DfsIterative(TreeNode* root) {
  vector<int> result;
  if (!root) return result;
  stack<TreeNode*> stk;
  stk.push(root);
  while (!stk.empty()) {
    TreeNode* node = stk.top();
    stk.pop();
    result.push_back(node->val);
    if (node->right) stk.push(node->right);
    if (node->left) stk.push(node->left);
  }
  return result;
}
```

---

## 8. Array vs linked list vs deque

**Linked list** wins when you have O(1) lookup (hashmap) to a node and need O(1) reinsertion — the LRU pattern.
**Deque** wins for "sliding window where elements expire from both ends" — monotonic queue.

**LRU Cache (LC 146)**
```cpp
class LRUCache {
 public:
  explicit LRUCache(int capacity) : capacity_(capacity) {}

  int Get(int key) {
    auto it = map_.find(key);
    if (it == map_.end()) return -1;
    list_.splice(list_.begin(), list_, it->second);
    return it->second->second;
  }

  void Put(int key, int value) {
    auto it = map_.find(key);
    if (it != map_.end()) {
      it->second->second = value;
      list_.splice(list_.begin(), list_, it->second);
      return;
    }
    if (static_cast<int>(list_.size()) == capacity_) {
      map_.erase(list_.back().first);
      list_.pop_back();
    }
    list_.emplace_front(key, value);
    map_[key] = list_.begin();
  }

 private:
  int capacity_;
  list<pair<int, int>> list_;
  unordered_map<int, list<pair<int, int>>::iterator> map_;
};
```

**Sliding Window Maximum (LC 239) — monotonic deque**
```cpp
vector<int> MaxSlidingWindow(const vector<int>& nums, int k) {
  deque<int> dq;  // indices, decreasing values
  vector<int> result;
  for (int i = 0; i < static_cast<int>(nums.size()); ++i) {
    while (!dq.empty() && nums[dq.back()] <= nums[i]) dq.pop_back();
    dq.push_back(i);
    if (dq.front() <= i - k) dq.pop_front();
    if (i >= k - 1) result.push_back(nums[dq.front()]);
  }
  return result;
}
```

---

## 9. unordered_map vs map

`unordered_map`: O(1) average, **O(n) worst case** under hash collisions — relevant if asked about adversarial input or production hardening.
`map`: O(log n) guaranteed, always sorted iteration order.

```cpp
unordered_map<int, int> freq;   // O(1) avg lookup, no ordering
map<int, int> ordered;          // O(log n) guaranteed, sorted keys
```

---

## 10. Monotonic stack

**Signal:** "next greater/smaller element," where each element is invalidated only by a *future* element (no expiry from a window — that's a monotonic queue instead).

**Daily Temperatures (LC 739)**
```cpp
vector<int> DailyTemperatures(const vector<int>& temps) {
  vector<int> result(temps.size(), 0);
  stack<int> stk;  // indices, decreasing temps
  for (int i = 0; i < static_cast<int>(temps.size()); ++i) {
    while (!stk.empty() && temps[stk.top()] < temps[i]) {
      result[stk.top()] = i - stk.top();
      stk.pop();
    }
    stk.push(i);
  }
  return result;
}
```

---

## 11. Interval scheduling

**The sort key determines the greedy proof.** Sort by start → merge overlaps. Sort by end → maximize non-overlapping count.

**Non-overlapping Intervals (LC 435)**
```cpp
int EraseOverlapIntervals(vector<vector<int>>& intervals) {
  sort(intervals.begin(), intervals.end(),
       [](const vector<int>& a, const vector<int>& b) {
         return a[1] < b[1];
       });
  int kept_end = INT_MIN, removed = 0;
  for (const auto& interval : intervals) {
    if (interval[0] >= kept_end) {
      kept_end = interval[1];
    } else {
      ++removed;
    }
  }
  return removed;
}
```

---

## 12. Trie vs hashmap

**Use a trie** when queries are prefix-based (autocomplete, "all words starting with X") or you're doing DFS-with-pruning over a dictionary (Word Search II). A hashmap can't answer "does any key start with this prefix" without scanning everything.

```cpp
class TrieNode {
 public:
  array<TrieNode*, 26> children{};
  bool is_word = false;
};

class Trie {
 public:
  Trie() : root_(new TrieNode()) {}

  void Insert(const string& word) {
    TrieNode* node = root_;
    for (char c : word) {
      int idx = c - 'a';
      if (!node->children[idx]) node->children[idx] = new TrieNode();
      node = node->children[idx];
    }
    node->is_word = true;
  }

  bool Search(const string& word) {
    TrieNode* node = Find(word);
    return node != nullptr && node->is_word;
  }

  bool StartsWith(const string& prefix) {
    return Find(prefix) != nullptr;
  }

 private:
  TrieNode* Find(const string& s) {
    TrieNode* node = root_;
    for (char c : s) {
      int idx = c - 'a';
      if (!node->children[idx]) return nullptr;
      node = node->children[idx];
    }
    return node;
  }

  TrieNode* root_;
};
```

---

## 13. Segment tree vs Fenwick tree vs plain prefix sum

- **Plain prefix sum array**: O(1) query, O(n) build, but O(n) to update — use when the array is static.
- **Fenwick tree (BIT)**: O(log n) point update + prefix query — use when sums change over time. Simple and compact, but naturally suited to invertible operations (sum, XOR), not min/max.
- **Segment tree**: O(log n) for arbitrary associative operations (min, max, gcd) with point or range updates — more general, more code.

**Fenwick tree (range sum with updates)**
```cpp
class FenwickTree {
 public:
  explicit FenwickTree(int n) : tree_(n + 1, 0) {}

  void Update(int i, int delta) {
    for (++i; i < static_cast<int>(tree_.size()); i += i & (-i)) {
      tree_[i] += delta;
    }
  }

  int Query(int i) {  // prefix sum over [0, i]
    int sum = 0;
    for (++i; i > 0; i -= i & (-i)) {
      sum += tree_[i];
    }
    return sum;
  }

 private:
  vector<int> tree_;
};
```

**Segment tree (range min, point update)**
```cpp
class SegmentTree {
 public:
  explicit SegmentTree(const vector<int>& data)
      : n_(static_cast<int>(data.size())), tree_(2 * n_) {
    for (int i = 0; i < n_; ++i) tree_[n_ + i] = data[i];
    for (int i = n_ - 1; i > 0; --i) {
      tree_[i] = min(tree_[2 * i], tree_[2 * i + 1]);
    }
  }

  void Update(int pos, int value) {
    pos += n_;
    tree_[pos] = value;
    for (pos /= 2; pos >= 1; pos /= 2) {
      tree_[pos] = min(tree_[2 * pos], tree_[2 * pos + 1]);
    }
  }

  int QueryMin(int left, int right) {  // half-open [left, right)
    int result = INT_MAX;
    for (left += n_, right += n_; left < right; left /= 2, right /= 2) {
      if (left & 1) result = min(result, tree_[left++]);
      if (right & 1) result = min(result, tree_[--right]);
    }
    return result;
  }

 private:
  int n_;
  vector<int> tree_;
};
```

---

## 14. Floyd's cycle detection family ("find duplicate")

Three approaches, pick based on constraints:
- **Hashset**: O(n) time, O(n) space, simplest — default unless told to optimize space.
- **Floyd's tortoise & hare**: O(n) time, O(1) space — works when the structure forms an implicit linked list (array values as "next pointers," LC 287; or actual linked lists, LC 142).
- **In-place marking**: O(1) space but mutates input — only valid when values are guaranteed in range [1, n] and mutation is acceptable.

**Linked List Cycle II (LC 142)**
```cpp
ListNode* DetectCycle(ListNode* head) {
  ListNode* slow = head;
  ListNode* fast = head;
  while (fast != nullptr && fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
    if (slow == fast) {
      ListNode* ptr = head;
      while (ptr != slow) {
        ptr = ptr->next;
        slow = slow->next;
      }
      return ptr;
    }
  }
  return nullptr;
}
```

**Find the Duplicate Number (LC 287) — Floyd's on array-as-linked-list**
```cpp
int FindDuplicate(const vector<int>& nums) {
  int slow = nums[0], fast = nums[0];
  do {
    slow = nums[slow];
    fast = nums[nums[fast]];
  } while (slow != fast);

  slow = nums[0];
  while (slow != fast) {
    slow = nums[slow];
    fast = nums[fast];
  }
  return slow;
}
```

**Find All Duplicates (LC 442) — in-place marking, O(1) space**
```cpp
vector<int> FindDuplicates(vector<int>* nums) {
  vector<int> result;
  for (int num : *nums) {
    int idx = abs(num) - 1;
    if ((*nums)[idx] < 0) {
      result.push_back(abs(num));
    } else {
      (*nums)[idx] = -(*nums)[idx];
    }
  }
  return result;
}
```
