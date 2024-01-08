## 1. Definitions
**Single-source Shortest-path**: given $G = (V, E)$ and source $s ∈ V$ want to find shortest  
path $s \rightarrow_p V$, $∀v ∈ V$  

**Single-destination Shortest-path**: given $G = (V, E)$ and target $t ∈ V$ want to find shortest  
path $u \rightarrow_p t$, $∀u ∈ V$  

**Single-pair Shortest-path**: given $G = (V, E)$ and pair of vertices $u$, $v ∈ V$ want to find  
shortest path $u \rightarrow_p v$  

**All-pair Shortest-path**: given $G = (V, E)$ want to find shortest path $u \rightarrow_p v$, $∀v ∈ V$  

**Note**: Single source is used to solve all variants
## 2. Properties
- The shortest path problem has optimal substructures, i.e. $v_0 → v_i → v_j → v_n$  
- Negative weights are allowed as long as there are no negative cycles  
- The shortest path will never contain a cycle

**Triangle Inequality**: $∀(u, v) ∈ E, δ(s, v) ≤ δ(s, u) + w(u, v)$

**Upper-bound property**: $d[v] ≥ δ(s, v) ∀v ∈ V$ and once $d[v] = δ(s, v)$ it never changes

**No-path property**: if $∄s →_p v$ then $d[v] = δ(s, v) = ∞$  

**Convergence property**: if $s → u → v$ is the shortest path in $G$, $∃u, v ∈ V . if d[u] = δ(s, u)$ prior to relaxing edge $(u, v)$ then $d[v] = δ(s, v)$ at all times afterwards.  

**Path-relaxation property**: if $p = ⟨v_0, v_1, . . . , v_k⟩$ is shortest path from $s = v_0 →_p v_k$ and paths are relaxed in order $(v_0, v_1), (v_1, v_2), . . . , (v_{k−1}, v_k)$ then $d[vk] = δ(s, vk)$  

**Predecessor-subgraph property**: Once $d[v] = δ(s, v)∀v ∈ V$, the predecessor subgraph is a  
shortest-paths tree rooted at $s$.

## 3. Shortest Paths Set up
- Given $G = (V, E)$ directed graph with weight function $w : E → R$ and a source $s ∈ V$.  
- If $p = ⟨v_0, v_1, . . . , v_k⟩$ is a path in $G$ then $w(p) = \sum^{k}_{i=1} w(v_{i-1}, v_i)$  
- Shortest-path weight:  
$$
δ(u, v) =  

\begin{cases}
	\text{min}\{w(p) : u \rightarrow_p v \} & \text{if } ∃u \rightarrow_p v \\
	\infty & \text{otherwise}
\end{cases}
$$
- Maintain $d[v]$ and $π[v]$  
	- $d[v]=\text{our estimate of } δ(s, v). (d[v] ≥ δ(s, v))$  
	- $π[v]=\text{predecessors in the shortest path from } s \text{ to } v$