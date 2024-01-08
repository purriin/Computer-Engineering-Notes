```c
void InsertionSort(int list[], int length) {
	for (int top = 1; top < length, top++) {
		int item = list[top];
		int emptyIndex = top;
		
		while (emptyIndex > 0 && list[emptyIndex - 1] > item) {
			list[emptyIndex] = list[emptyIndex - 1];
		}
		
		emptyIndex--;
	}
}
```