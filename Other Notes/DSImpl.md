### Linked List
```
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
	ListNode() {
		this.val = -1;
		this.next = NULL;
	}
    ListNode(int val) {
		this.val = val;
		this.next = NULL;
	}
};
```

### Graph definition
```
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
```