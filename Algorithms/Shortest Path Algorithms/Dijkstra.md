**Pseudocode**:
```c
function Dijkstra(G,w,s)  
	Init-Single-Source(G,s)  
	S = ∅ // set of vertices whose rst shortest path weights have been determined  
	Q = ∅  
	for each v ∈ V do  
		INSERT(Q, v) // priority queue sorted by d[v]  
	while Q ̸ = ∅ do  
		u = EXTRACT-MIN(Q)  
		S = S ∪ {u}  
		for each v ∈ adj[u] do  
			Relax(u, v, w) // priority queue sorted by d[v]  
			Decrease-key(Q, v)  
```
#### Dijkstra Key Take-Aways
- Assume G contains no negative weight edges  
- **Running time**:  
	- **Q list**: $O(V 2 + E) = O(V 2)$
	- **Q priority queue (heap)**: $O((V + E) log V ) = O(E log V)$ (if sufficiently sparse, $E = O(V 2/ log V ))$
	- **Q Fibonacci heap**: $O(V log V + E)$
- Dijkstra is a greedy algorithm, always choosing the next lowest-weight cycle