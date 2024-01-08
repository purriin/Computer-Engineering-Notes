**Pseudocode**:
```c
/* Runtime: O(h) = O(logn)
 * Enforces the heap order property if it is violated 
 */ 
function Max-Heapify(A, i)  
	Compare A[i] with A[2i] and A[2i + 1]  
	if A[i] is smaller then  
		swap A[i] ↔ max(A[2i], A[2i + 1])  
	end if  
	Recurse downwards, until property is not violated or hit a leaf node  
end function

/* Runtime: O(nlogn)
 * Actual: Time to run Max-Heapify is linear in the height of the node it is run on and most node have smaller height
 */
function Build-Max-Heap(A, n)
	for each i = [n/2] : 1 do
		Max-Heapify(A, i)
	end for
end function

// Runtime: O(n) + O(nlogn) = O(nlogn)
function Extract-Max(A)
	Swap A[1] ↔ A[A.size()]
	A.size = A.size - 1
	Max-Heapify(A, 1)
end function

function Heapsort(A,n)
	Build-Max-Heap(A,n)
	for each i = n : 2 do
		Extract-Max(A)
	end for
end function
```

- At height *h*, there are at most $\frac{n}{2^(h+1)}$ nodes
- Height of heap is $logn$
- For Build-Max-Heap:
	- Runtime: $\sum_{h=0}^{logn} \frac{n}{2^(h+1)}O(h) <= cn \sum_{h=0}^{\infty}\frac{h}{2^(h + 1)} = ... = O(n)$

#### Heap Sort Definition & Properties
**Heap**: is an array $A = [a_1, a_2, ... , a_n]$ of elements such that:
1. **Heap shape property**: the heap is an almost complete binary tree (except the last row is full)
2. **Heap order property:**
	- Max-heap: $∀i, A[parent(i)] ≥ A[i]$  
	- Min-heap: $∀i, A[parent(i)] ≤ A[i]$
- Indexing parents and children (Assume index of array starts at 1)
	- parent(i) = $\frac{i}{2}$
	- left(i) = $2i$
	- right(i) = $2i + 1$

#### Heap Sort Key Take-Aways
- $2h ≤ n ≤ 2h+1 − 1 ⇔ h = ⌊log n⌋$
- For a max-heap, the maximum value will always occur at the root  
- For a min-heap, the minimum value will always occur at the root  
- Heapsort is $Θ(n log n)$ in time and $Θ(1)$ in space (space complexity only considers extra memory needed excluding input)  
- Heapsort is an in-place algorithm  
= Heapsort is not stable  
**Note**: A stable sorting algorithm is a sorting algo that preserves the relative order of the same value in the previous step 