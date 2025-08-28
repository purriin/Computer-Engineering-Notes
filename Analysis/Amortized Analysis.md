### 1. Definitions
**Average cost**: mean over all possible input costs  

**Expected cost**: expectation over all possible input costs w.r.t. a distribution of the input. (If distribution is uniform, then expected is the same as average)  

**Amortized cost**: average cost over a sequence of inputs or operations in the worst case
### 2. Aggregate Analysis
1. Given some operations/functions $f_i(x)$ and a sequence $(x_1, x_2, ..., x_n)$  
2. Determine $\sum^n_{i=1} c(f_i(x_i)) = T(n)$ the total running time to execute the entire sequence  
3. The amortized cost (cost per operation) is $T (n) / n$
### 3. Accounting Method
1. Declare that $\$\overset{\land}{c}_i$ will be charged for each operation/function.  
2.  Describe a procedure for how the $\$\overset{\land}{c}_i$ will be allocated  
3. Assert a credit invariant  
4. Argue/prove the credit invariant  
5. Using the credit invariant, argue why the credit never goes negative  
6. The amortized cost of each operation $i$ is $O(\overset{\land}{c}_i$)