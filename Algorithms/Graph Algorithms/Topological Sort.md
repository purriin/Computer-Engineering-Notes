**Pseudocode**:
```c
function TopologicalSort(G)
	DFS(G)
	Output vertices in decreasing order of finish time
```

#### Topological Sort Key Take-Aways
- Produces total ordering from partial ordering
- $G = (V, E)$ must be a DAG to produce a valid topological sort
- **Runtime**: $O(V + E)$  
- Can convert undirected graph to a directed graph, ∀(u, v) ∈ E do the following transformation
	- Number of edges doubles, which shouldn't change asymptotic behavior