## 1. Definition
- Let $U$ be the universe, $K ⊂ U$ a set of keys. $T$ a table of size $m$ with indices {$0, ..., m − 1$}  
- A hash function $h →$ {$0, ..., m − 1$} maps objects in the universe to the indices (hashes key $k ∈ K$ to index $h(k)$)  

Good hashing scheme:  
- Simple uniform hashing: any given element is equally likely to be hashed into any of the m slots.  
- Good mechanism for collision resolution (since $m < |U |$, there could be collision)  

## 2. Chained Hash Tables
- Instead of directly storing the values in the array, each slot contains a linked list
- To insert a key $k$, append it to the linked list at $h(k)$
- To delete, remove it from the linked list 
- Load factor $α = n/m$, where $n =$ # elements in the hash table, $m =$ size of the array
- With the simple uniform hashing assumption, average length of the linked list is $α$  
- Expected runtime: $O(1 + α) = O(1 + n/m)$
- If we want $O(log n)$ instead of $O(n)$, use RB Tree instead of linked list.
## 3. Open Addressing
- Use hash function of the form $h(k, i)$, try to insert $k$ at $h(k, 0), h(k, 1), ...,$ until we find an empty slot  

**Linear probing**: $h(k, i) = (h′(k) + i)$ mod $m$  

**Quadratic probing**: $h(k, i) = (h′(k) + c_1i + c_2i^2)$ mod $m$ (slightly better performance, but  
still m probing sequences)  

**Double hashing**: $h(k, i) = (h_1(k) + ih_2(k))$ mod $m$  
- No clusters  
- $h_2(k)$ must be relatively prime to $m (gcd(h_2(k), m) = 1)$ for all $k$  
- Can choose $m$ prime, $h2(k) < m, ∀k$
- $Θ(m^2)$ different probe sequences