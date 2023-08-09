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