**Pseudocode**:
```c
/* Input: A[1,...,n], A[j] E {1,...k}
 * Output: B[1,...,n] sorted
 * Create an auxiliary array C[1,..,k], recording the number of elements in A with value <= x
 */
function Counting-Sort(A[1,..,n], B[1,..,n], n, k)
	for each i = 0 : k do
		C[i] = 0
	end for
	for each j = 1 : n do
		C[A[j]] = C[A[j]] + 1 //# elements in A with value x
	end for
	for each i = 1 : k do
		C[i] = C[i] + C[i - 1] //# elements in A with value <= x
	end for
	for each j = n : 1 do // iterate A backwards
		B[C[A[j]]] = A[j]  // C[A[j]] implies the sorted position of A[j]
		C[A[j]] = C[A[j]] - 1
	end for
end function
```

#### Counting Sort Key Take-Aways
- Counting sort assumes elements are integers ranging from 0 to k
- **Runtime**: $\theta(n + k)$ (if k = $O(n)$, then runtime is $\theta(n))$
- Counting sort uses no comparisons (it uses values of elements to determine the position)
- Counting sort is not in-place
- Counting sort is stable