#### BFS Key Take-Aways
- Discover all nodes of depth $k$ before discovering any node of depth $k + 1$  
- We only discover nodes reachable from $s (i.e. ∃ s →_p t)$  
- **Runtime**: $O(V + E)$  
- **Node types**: WHITE (undiscovered), GREY (processing), BLACK (nished)  
- Maintain $v.d/d[v]$ and $v.π/π[v]$, $∀v ∈ V$  
	- $∀v ∈ V , d[v] =$ min{#edges in any path from $s$ to $v$}  
	- if $∄ s →_p v, d[v] = ∞$  
	- $π[v]$ is the predecessor of $v$ on the shortest path $s → v$  
- BFS can be thought of as a SSSP algorithm for a graph with all edge weights equal to $1$. 
$d[v] = δ(s, v)$. 