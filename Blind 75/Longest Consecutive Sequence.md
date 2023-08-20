[Link](https://leetcode.com/problems/longest-consecutive-sequence)

```
Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.

 

Example 1:

Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
Example 2:

Input: nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9
 

Constraints:

0 <= nums.length <= 105
-10^9 <= nums[i] <= 10^9
```

#### Using hashmap

```cpp
signed int LIMIT = -1000000000;
int longestConsecutive(vector<int>& nums) {
    int sol = 0;
    unordered_map<int, int> seq_map;
    for(int n: nums) {
        seq_map[n] = 1;
    }

    for(auto it = seq_map.begin(); it != seq_map.end(); it++) {
        int curr = it->first;
        while(curr-- >= LIMIT && seq_map.count(curr)) {
            seq_map[it->first] += seq_map[curr];
            seq_map.erase(curr);
        }
        sol = max(sol, it->second);
    }
            
    return sol;
}

```


#### Union Find: Graph Algorithm

Time: O(n*logn), in this case O(n)
Space: O(n)

```cpp
unordered_map<int, int> root;
void Union(int x, int y) {
    int root_x = find(x);
    int root_y = find(y);
    root[root_y] = root_x;
}
    
int find(int x) {
    if(root[x] == x) {
        return x;
    } else return find(root[x]);
}

int longestConsecutive(vector<int>& nums) {
    //1. init parent hashmap
    for(int n: nums) root[n] = n;
    
    //2. Connect num and num + 1 with one root
    for(int n: nums) {
        if(root.count(n + 1) != 0) {
            Union(n, n + 1);
        }
    }

    //3. Count all the component having the same root
    //find te root using find function
    unordered_map<int, int> n_nodes;
    int sol = 0;
    for(auto it = root.begin(); it != root.end(); it++) {
        int root_n = find(it->first);
        n_nodes[root_n]++;
        sol = max(sol, n_nodes[root_n]);
    }
    return sol;
}
```
