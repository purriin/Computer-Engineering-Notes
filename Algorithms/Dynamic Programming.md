## 1. Definition
**Simple definition**: Efficient recursion for solving well-behaved optimization problems  

**Optimization problems**: trying to nd an optimal solution given some constraints (often discrete problems)  

- Used for problems with the following properties  
	- **Optimal substructure**: optimal solutions incorporate optimal solutions to related subproblems  
	- **Overlapping subproblems**: solving the same subproblems over and over again (Memorization exploits this redundancy)  

- In general, DP good for tasks with small but repetitive search spaces  

## 2. Methodology
  
1. Visualize Examples (start small, with base cases, and gradually increase the problem size)  
2. Determine how the problem exhibits optimal substructure, i.e. characterize the structure of an optimal solution  
3. Find a relationship among subproblems, i.e. defining the rule of an optimal solution recursively in terms of the optimal solution to subproblems  
4. Compute the value of an optimal solution, typically bottom-up solving subproblems in order and use memorization  
5. Construct the optimal solution from the computed information (if you want more than just the value of the optimal solution, you can still use memory)