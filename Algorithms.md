## Graph Related algorithms
[NeetCode Video](https://www.youtube.com/watch?v=utDu3Q7Flrw&t=3s)

#### Union Find
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

Complexity: Time: O(v + e). Space: Recursion stack: O(v)
### Dijkstra's


### Cycle Detection
The key is to 
### Prim's or Kruskal

### Floyd Warshall

## Arrays

### Binary Search
    
A Video: [Link](https://www.youtube.com/watch?v=1IOp0jyu128&ab_channel=AndyDudley) → watch this video, that’s it

Notes from the video:

- It takes `logn` time to answer the following
- where is the item in the sorted array
    
    ```cpp
    while(l<=r) {
        if(arr[mid] == target) return mid;
        if(arr[mid] <= target) l = mid + 1; 
        else r = mid - 1;
    }
    ```
    
- what is the smallest number greater than the target number
    
    ```cpp
    while(l<=r) {
            // all the elements <= target are going to be left side of l ptr
        if(arr[mid] <= target) l = mid + 1;
        // all elments greater than target are on the right side of r
        else r = mid - 1;   
        return l;                
    }
    ```
    
- what is the largest number smaller than the target number
    
    ```cpp
    while(l<=r) {
        if(arr[mid] < target) l = mid + 1;  
        // all the elements <= target are going to be right side of r ptr
        else if(arr[mid] >= target ) r = mid - 1; 
        return r;
    }
    ```
    
- what index this item can be inserted
- notes
    - Left and right pointer

### Quick Select
<details>
 <summary>QuickSelect</summary>
average case O(n), worst case O(n^2)
    
YouTube Link: [Here](https://www.youtube.com/watch?v=XEmy13g1Qxc&ab_channel=NeetCode)
    
- Algorithm
    
    ```cpp
    int quickSelect(int left, int right) {
        int size = right - left + 1;

        //1. Select a pivot and swap with the right ptr
        int pivotIdx = rand()%size + left;
        int pivot = nums[pivotIdx];
        swap(right, pivotIdx);

        //2. Create a ptr to point to the last index + 1 of the numbers <= pivot element
        // this is a partition ptr
        int partIdx = left;
        for(int i = left; i < right; i++) {
            if(nums[i] <= pivot) {
                swap(partIdx++, i);
            }
        }

        //3. Swap the pivot element, with the ptr maintained above create for partition
        swap(partIdx, right);

        return partIdx;
    }
    ```
    
- kth index element
    
    ```cpp
    int quickSelectUtil(int targetIdx , int left, int right) {
            int partIdx = quickSelect(left, right);
    
            if(partIdx == targetIdx) {
                return nums[partIdx];
            } else if(partIdx < targetIdx) {
                return quickSelectUtil(targetIdx, partIdx + 1, right);
            } else {
                return quickSelectUtil(targetIdx, left, partIdx - 1);
            }
        }
    ```
    
Source: StackOverflow

- Question
    
    According to [Wikipedia](http://en.wikipedia.org/wiki/Selection_algorithm), partition-based selection algorithms such as quickselect have runtime of `O(n)`, but I am not convinced by it. Can anyone explain why it is `O(n)`?
    
    In the normal quick-sort, the runtime is `O(n log n)`. Every time we partition the branch into two branches (greater than the pivot and lesser than the pivot), we need to continue the process in **both** branches, whereas quickselect only needs to process **one** branch. I totally understand these points. However, if you think in the Binary Search algorithm, after we chose the middle element, we are also searching only **one** side of the branch. So does that make the algorithm `O(1)`? *No*, of course, the Binary Search Algorithm is still `O(log N)` instead of `O(1)`. This is also the same thing as the search element in a Binary Search Tree. We only search for **one** side, but we still consider `O(log n)` instead of `O(1)`.
    
    Can someone explain why in quickselect, if we continue the search in *one* side of pivot, it is considered `O(1)` instead of `O(log n)`? I consider the algorithm to be `O(n log n)`, `O(N)` for the partitioning, and `O(log n)` for the number of times to continue finding.
    
- Answer
    
    There are several different selection algorithms, from the much simpler quickselect (expected O(n), worst-case O(n2)) to the more complex median-of-medians algorithm (Θ(n)). Both of these algorithms work by using a quicksort partitioning step (time O(n)) to rearrange the elements and position one element into its proper position. If that element is at the index in question, we're done and can just return that element. Otherwise, we determine which side to recurse on and recurse there.
    
    Let's now make a very strong assumption - suppose that we're using quickselect (pick the pivot randomly) and on each iteration we manage to guess the exact middle of the array. In that case, our algorithm will work like this: we do a partition step, throw away half of the array, then recursively process one half of the array. This means that on each recursive call we end up doing work proportional to the length of the array at that level, but that length keeps decreasing by a factor of two on each iteration. If we work out the math (ignoring constant factors, etc.) we end up getting the following time:
    
    - Work at the first level: n
    - Work after one recursive call: n / 2
    - Work after two recursive calls: n / 4
    - Work after three recursive calls: n / 8
    - ...
    
    This means that the total work done is given by
    
    > n + n / 2 + n / 4 + n / 8 + n / 16 + ... = n (1 + 1/2 + 1/4 + 1/8 + ...)
    > 
    
    Notice that this last term is n times the sum of 1, 1/2, 1/4, 1/8, etc. If you work out this infinite sum, despite the fact that there are infinitely many terms, the total sum is exactly 2. This means that the total work is
    
    > n + n / 2 + n / 4 + n / 8 + n / 16 + ... = n (1 + 1/2 + 1/4 + 1/8 + ...) = 2n
    > 
    
    This may seem weird, but the idea is that if we do linear work on each level but keep cutting the array in half, we end up doing only roughly 2n work.
    
    An important detail here is that there are indeed O(log n) different iterations here, but not all of them are doing an equal amount of work. Indeed, each iteration does half as much work as the previous iteration. If we ignore the fact that the work is decreasing, you can conclude that the work is O(n log n), which is correct but not a tight bound. This more precise analysis, which uses the fact that the work done keeps decreasing on each iteration, gives the O(n) runtime.
    
    Of course, this is a very optimistic assumption - we almost never get a 50/50 split! - but using a more powerful version of this analysis, you can say that if you can guarantee *any* constant factor split, the total work done is only some constant multiple of n. If we pick a totally random element on each iteration (as we do in quickselect), then on expectation we only need to pick two elements before we end up picking some pivot element in the middle 50% of the array, which means that, on expectation, only two rounds of picking a pivot are required before we end up picking something that gives a 25/75 split. This is where the expected runtime of O(n) for quickselect comes from.
    
    A formal analysis of the median-of-medians algorithm is much harder because the recurrence is difficult and not easy to analyze. Intuitively, the algorithm works by doing a small amount of work to guarantee a good pivot is chosen. However, because there are two different recursive calls made, an analysis like the above won't work correctly. You can either use an advanced result called the [Akra-Bazzi theorem](http://en.wikipedia.org/wiki/Akra%E2%80%93Bazzi_method), or use the formal definition of big-O to explicitly prove that the runtime is O(n). For a more detailed analysis, check out "Introduction to Algorithms, Third Edition" by Cormen, Leisserson, Rivest, and Stein.
</details>


## Array Related Algorithms

### Merge Sort
### Usages

- Sorting linked lists
- Very good choice for large lists

### Properties

- Stable algorithm

### Complexity

- Time: O(n log n) → Best, Average and Worst
- Space: O(n) → a temporary array is created to merge two sorted lists.

### Code:
```cpp
//1. find the mid element
//2. sort left -> mid and mid + 1 -> right
//3. merge sorted arrays using a temporary array: merge function

void mergeSort(int left, int right) {
    if(left >= right) return;
    int mid = (left + right)/2;

    mergeSort(left, mid);
    mergeSort(mid + 1, right);

    merge(left, mid, right);
}

void merge(int left, int mid, int right) {
        //Goal: merge two sorted array left: mid and mid + 1: right
        int size = (right - left + 1);

        int i = left; //left: mid;
        int j = mid + 1; //mid+1:right;
        vector<int> tmp;

        while(i <= mid && j <= right) {
            //descending order
            int leftEle = nums[i];
            int rightEle = nums[j];

            if(leftEle <= rightEle) {
                tmp.push_back(rightEle);
                j++;
            } else {
                tmp.push_back(leftEle);
                i++;
            }
        }

        //step 2. copying remaining elements
        while(i <= mid) {
            tmp.push_back(nums[i++]);
        }

        while(j <= right) {
            tmp.push_back(nums[j++]);
        }

        //step 3. tranfering tmp values to original array
        for(int i = 0; i < tmp.size() && (i + left) < nums.size(); i++) {
            nums[i + left] = tmp[i];
        }
    }
```


