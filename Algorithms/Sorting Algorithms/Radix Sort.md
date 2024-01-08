**Pseudocode**:
```c
function Radix-Sort(A,d) // Sort least sig digit first
	for each i = 1 : d do
		Stable sort A on digit i // Relative order in previous step is preserved
	end for
end function
```

#### Radix Sort Key Take-Aways
- Radix sort assumes all elements have ≤ d-digits  
- Runtime $Θ(d(n + k))$ for d-digit numbers and each digit ∈ [0, k]  
- Runtime $Θ(\frac{b}{r}(n + 2^r))$ for b-bit numbers and $r = min(b, ⌈log n⌉)$  
- Overall, it sorts in Θ(n)  
- Radix sort uses no comparisons  
- Radix sort is not in-place  
- Radix sort is stable