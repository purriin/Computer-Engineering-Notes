```c
void BubbleSort(int list[], int length) {
	int top, i;
	bool sortest = false;
	
	for (top = length - 1; top > 0 && !sorted; top--) {
		sorted = true;
		for (i = 0; i < top; i++) {
			if (list[i] > list[i + 1]) {
				int temp = list[i];
				list[i] = list[i + 1];
				list[i + 1] = temp;
				sorted = false;
			}
		}
	}
}
```