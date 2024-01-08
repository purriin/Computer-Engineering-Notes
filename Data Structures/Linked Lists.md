**Linear Lists**: A collection of items, an interface (API, functions), create a new empty list, insert in the list (beginning, end or middle)
- An array, memory copy is slow but random access

**Linked Lists**: Uses pointers, beginning is called *head* and the end is called *tail*, each element is a *node*, no memory copy, each node is **8 bytes** but fast insertion

- Item data structures all created on the heap

```c
typedef struct node {
	int data;
	struct node* next;
} Node;
```
```c
typedef struct linkedList {
	Node* head;
} LinkedList;
```

**Creating a Node**:
```c
Node* createNode(int data) {
	Node* newNode = malloc(sizeof(Node));
	
	if (newNode != NULL) { // Guard Condition
		newNode->data = data;
		newNode->next = NULL;
	}
	
	return newNode;
}
```

**Front Insert**:
```c
bool insertAtFront(Node** head, int data) {
	Node* newNode = createNode(data);
	
	if (newNode == NULL) {
		return false;
	}
	
	newNode->next = *head;
	*head = newNode;
	return true;
}
```

**Initialize List**:
```c
void initList(LinkedList* list) {
	list->head = NULL;
}
```

**Check if Empty**:
```c
bool isEmpty(LinkedList *list) {
	return list->head == NULL;
}
```

**Print List**:
```c
void printList(LinkedList* list) {
	Node* current = list->head;
	
	while (current != NULL) {
		printf("%d", current->data);
		current = current->next;
	}
}
```

**Find First Node**:
```c
Node* findFirstNode(LinkedList* list, int data) {
	Node* current = list->head;
	
	while (current != NULL) {
		if (current->data == data) {
			return current;
		} else {
			current = current->next;
		}
	}
	
	return NULL;
}
```

**Back Insert**:
```c
bool insertAtBack(LinkedList* list, int data) {
	if (isEmpty(list)) {
		return insertAtFront(list, data);
	}
	
	Node* current = head;
	
	while (current->next != NULL) {
		current = current->next;
	}
	
	current->next = createNode(data);
	
	if (current->next == NULL) {
		return false;
	} else {
		return true;
	}
}
```

**Middle Insert**:
```c
bool insertAtMiddle(LinkedList* list, int data) {
	Node* current = list->head;
	
	while (current->next != NULL && current->next->data < data) {
		current = current->next;
	}
	
	if (isEmpty(list) || current->data > data) {
		return insertAtFront(list, data);
	} else {
		Node* temp = current->next;
		current->next = createNode(data);
		
		if (newNode == NULL) {
			return false;
		}
		
		current->next->next = temp;
		return true;
	}
}
```

**Front Delete**:
```c
void deleteFront(LinkedList* list) {
	if (isEmpty(list)) {
		return;
	}
	
	Node* newHead = list->head->next;
	free(list->head);
	list->head = newHead;
}
```

**Back Delete**:
```c
void deleteBack(LinkedList* list) {
	if (isEmpty(list)) {
		return;
	}
	
	if (list->head->next == NULL) {
		deleteFront(list);
	}
	
	while (current->next->next != NULL) {
		current = current->next;
	}
	
	free(current->next);
	current->next = NULL;
}
```

**Delete All**:
```c
int deleteAllNodes(LinkedList* list) {
	int numDeleted = 0;
	
	while (!isEmpty(list)) {
		deleteFront(list);
		numDeleted++;
	}
	
	list->head = NULL;
	return numDeleted;
}
```

**Delete First Match**:
```c
bool deleteFirstMatch(ListedList* list, int data) {
	Node* current = list->head;
	
	if (current == NULL) {
		return false;
	}
	if (current->data == data) {
		deleteAtFront(list);
		return true;
	}
	
	while (current->next != NULL && current->next->data != data) {
		current = current->next;
	} 
	
	if (current->next != NULL) {
		Node* temp = current->next;
		current->next = temp->next;
		
		free(temp);
		return true;
	}
	
	return false;
}
```

**Delete All Matches**:
```c
int deleteAllMatches(LinkedList* list, int data) {
	int numDeleted = 0;
	
	while (deletefirstMatch(list,data)) {
		numDeleted++;
	}
	
	return numDeleted;
}
```