## 1. Definition
- $f (n) = O(g(n)) ⇔ ∃c, n_0 > 0 s.t. 0 ≤ f (n) ≤ c_g(n), ∀n ≥ n_0$  
- $f (n) = Ω(g(n)) ⇔ ∃c, n_0 > 0 s.t. 0 ≤ c_g(n) ≤ f (n), ∀n ≥ n_0$  
- $f (n) = Θ(g(n)) ⇔ f (n) = O(g(n)) and f (n) = Ω(g(n) ⇔ ∃c_1, c_2, n_0 > 0 s.t.$
	$0 ≤ c_1g(n) ≤ f (n) ≤ c_2g(n), ∀n ≥ n_0$

## 2. Properties
**Transitivity**: $f (n) = Θ(g(n)) and g(n) = Θ(h(n)) ⇒ f (n) = Θ(h(n))$

**Transpose**: $f (n) = O(g(n)) ⇔ g(n) = Ω(f (n))$

**Symmetry**: $f (n) = Θ(g(n)) ⇒ g(n) = Θ(f (n))$

## 3. Limit Method
- $\lim_{n→∞}f (n) / g(n) = 0 ⇒ f (n) = o(g(n))$ 
- $\lim_{n→∞}  f (n) / g(n) = c ∈ [0, ∞) ⇒ f (n) = O(g(n))$
- $\lim_{n→∞}  f (n) /g(n) = c ∈ (0, ∞) ⇒ f (n) = Θ(g(n))$
- $\lim_{n→∞ } f (n) /g(n) = c ∈ (0, ∞] ⇒ f (n) = Ω(g(n))$
- $lim_{n→∞} f (n) /g(n) = ∞ ⇒ f (n) = ω(g(n))$
## 4. Bounded Functions
**Polylogarithmically bounded**:$ $∃k > 0, f (n) = O((log n)^k)$
**Polynomially bounded**: $∃k > 0, f (n) = O(n^k)$
**Exponentially bounded**: $∃k > 0, f (n) = O(k^n)$
  
- $f(n) = O(nk) ⇔ log(f (n)) = O(log n)$

- All Logarithmically bounded functions are polynomically bounded. i.e. $f (n) = O((log n)^a) ⇒ f (n) = O(n^b), ∀a, b ≥ 0$

- All polynomially bounded functions are exponentially bounded. i.e. $f (n) = O(na) ⇒ f (n) = O(b^n), ∀a > 0, b > 1$