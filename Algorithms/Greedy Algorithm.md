**Simplified Definition**: an algorithm where locally optimal decisions lead to a globally optimal solution  

**Idea**: when making a choice, take the one that looks the best right now. Local optimality leads to global optimality

**Note**: Greedy is not always optimal, but good as approximation algorithms

**Properties**:  
- **Greedy choice property**: the optimal solution agrees with the first greedy choice
- **Smaller subproblems**: after making the greedy choice, the resulting subproblem reduces in size
- **Optimal substructure**: an optimal solution contains optimal solutions to subproblems  

**Types**:
1. **Car building**: rearrange the full set of tasks to minimize the total penalty  
2. **Scheduling**: find a feasible subset of tasks to maximize the profit