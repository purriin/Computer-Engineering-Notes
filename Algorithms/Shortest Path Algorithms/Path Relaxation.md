**Pseudocode**:
```c
function Relax(u,v,w)
	d[v] = min(d[v], d[u] + w(u,v))
```

```c
function Relax(u,v,w)  
	if d[v] > d[u] + w(u, v) then  
		d[v] = d[u] + w(u, v)  
		π[v] = u
```
