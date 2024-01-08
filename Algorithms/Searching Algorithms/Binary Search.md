**Without Recursion**:
```c
int BinarySearch(int list[], int length, int data) {
	int low = 0, high = length - 1;
	
	while(low <= high) {
		int middle = (low + high)/2;
		
		if (list[middle] == data) {
			return middle;
		} else if (data < list[middle]) {
			high = middle - 1;
		} else {
			low = middle + 1;
		}
	}
	
	return -1;
}
```

**With Recursion**:
```c
int binarySearchHelper(int list[], int data, int low, int high) {
	if (low > high) {
		return -1;
	}
	
	int middle = (low + high)/2;
	
	if (list[middle] == data) {
		return middle'
	}
	
	if (data < list[middle]) {
		return binarySearchHelper(list, data, low, middle - 1);
	} else {
		return binarySearchHelper(list, data, middle + 1, high);
	}
}
```
