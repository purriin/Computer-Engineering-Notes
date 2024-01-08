## 1. Master Theorem
$T (n) = aT(n/b) + f (n)$, where $a ≥ 1, b ≥ 1, f (n)$ is asymptotically positive

1. **Case 1**: $f (n) = O(n^{log_b a−ε}), ε > 0$. Then $T (n) = Θ(n^{log_b a})$ (cost of solving the sub-problems at each level increases by a certain factor, the last level dominates)  

2. **Case 2**: $f (n) = Θ(n^{log_b a})$. Then $T (n) = Θ(n^{log_b a} log n)$ (cost to solve subproblem at each level is nearly equal)  

3. **Case 3**: $f (n) = Ω(n^{log_b a+ε}), ε > 0$ and $af(n/b) ≤ cf (n)$ for some $c < 1$ and $n > n_0$ (regularity condition, always holds for polynomials). Then $T (n) = Θ(f (n))$ (cost of solving the subproblems at each level decreases by a certain factor)