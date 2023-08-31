### Linked List
```cpp
class ListNode {
	unsigned int val;
	ListNode* next;
	public:
	ListNode* nextNode(ListNode* curent) {
		if(current != NULL) {
			return current->next;
		} else {
			return NULL;
		} 
	}
	//add constructors as per the needs
};
```

### Graph definition

#### 1. Undirected unweighted graph

```cpp
//simple and useful
unordered_map<int, vector<int>> adjList;
```
```cpp
//alternative
class Node {
public:
    int val;
    vector<Node*> neighbors;
};
```

