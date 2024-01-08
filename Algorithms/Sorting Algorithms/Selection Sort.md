```c
void SelectionSort(int list[], int length) {
	for (int top = length - 1; top > 0; top--) {
		int indexOfLargest = 0;
		for (int i = 1; i <= top; i++) {
			if (list[i] > list[indexOfLargest]) {
				indexOfLargest = i;
			}
		}
		swap(&list[top, &list[indexOfLargest]]);
	}
}
```