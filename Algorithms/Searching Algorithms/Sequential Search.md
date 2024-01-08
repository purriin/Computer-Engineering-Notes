```c
int SequentialSearch(int list[], int length, int data) {
	for (int i = 0; i < length; i++) {
		if (list[i] == data) {
			return i;
		}
	}
	return -1;
}
```