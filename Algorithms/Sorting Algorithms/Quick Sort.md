**Pseudocode**:
```c
Quick_Sort(in, left, right)
	pivot = RAND partition(in, left, right)
	if (pivot > left)
		Quick_Sort(in, left, pivot)
	if (pivot < right)
		Quick_Sort(in, pivot + 1, right)
end of Quick_Sort

// Takes O(n) time:
int partition(in, left, right)
	ls = left
	pivot = in[left]
	for (i = left + 1 ... right)
		if (in[i] <= pivot)
			ls = ls + 1
			swap(in[i], in[ls])
	swap(in[left], in[ls])
	return ls
end of partition

/* We want a O(n) vs O(n) right/left partition
 * To avoid worst case (sorted array) - randomly swap numbers 
 */
int Rand-Partition(in, left, right)
	i = random[left, right]
	swap(in[left], in[i])
	return partition
end of Rand-Partition
```

#### In Words:
1. **Pick a Pivot**: First, you choose one of the numbers in your list to be the "pivot." It's like a reference point.
2. **Divide the List**: Then, you separate the other numbers into 2 groups: 1 with numbers smaller than the pivot, and the other numbers larger than the pivot.
3. **Sort the Groups**: Now you do the same thing to the groups you just made. You pick a pivot for each of them and divide them into smaller groups.
4. **Repeat**: You keep doing this until you have tiny groups with only one or zero numbers in them. These tiny groups are already sorted because they only have 1 number.
5. **Put it Back Together**: Finally, you put all the tiny groups back together. Now your whole list is sorted.
#### Quick Sort Run-time:
**Worst Case**: θ$(n^2)$
	When array is sorted already:
	$T(n) = T(n-1) + \theta(n)$
		     $= T(n-2) + \theta(n-1) + \theta(n)$
		     $= \sum_{k=1}^{n} k + \theta(n)$
		     $= \theta(n^2)$
**Best Case:** θ$(nlogn)$
	When array is evenly split:
	$T(n) = 2T(\frac{n}{2}) + \theta(n)$
	         $= \theta(nlogn)$
**Average Case**:  θ$(nlogn)$
	As the average leans to worst case, constant gets worse:
	$(\frac{7}{8})^h*n = 1$
	$h = \log_{\frac{8}{7}} n$
	$T(n) = T(\frac{n}{8}) + T(\frac{7n}{8}) + \theta(n)$
	        $= h*\theta(n)$
	        $= \theta(nlogn)$

#### Quick Sort Key Take-Aways
- Worst case run time: Θ(n2) (the list is already sorted)
	- Pivot always the largest/smallest. Everytime we get one empty array and one array of size $n − 1$. $T (n) = T (n − 1) + O(n)$
- Best case run time: $Θ(n log n)$
	- Pivot always median, T (n) = 2T (n/2) + O(n)  
- Average/Expected run time: $Θ(n log n)$  
	- $T (n) = T (an) + T (bn) + O(n)$, where $a + b = 1$ as we have to run through the full array.  
- Quicksort tends to have the smallest constant in front of its runtime  
- Quicksort is not stable (although can modify to be stable)  
- Quicksort is in-place (although recursion stores stuff on stack, based on exact definition of in-place)  
- The idea of a randomized algorithm. Why randomization helps and how to analyze a random algorithm
- If we randomly shuffle input or choose pivot, we reduce the chance of getting the worst case scenario
	- The worst case scenario is still $O(n^2)$, but the chance is lower