## 4 - Constraint Satisfaction

- Map Coloring Problem
    - Given map and regions, how to assign minimum # of colors s.t. each region has a color and no adjacent regions share color?
    - Can model as graph w/ edges as dependencies
- Constraint Types - unary (e.g. in map coloring, each state has single variable. Unary constraint depends on single variable), binary (e.g. in map coloring, adjacent states cannot have some color. Binary constraint affects two variables), 3+ variables, soft constraints (e.g. preferences)
- Backtracking Search idea: model as search tree and try all possibilities
    - Idea: run into problems sooner rather than later to improve search
    - Optimizations:
        - assign variable to Least Constraining Value
        - change order of variable assignment based on Minimum Remaining Values (MRV) (if tie, pick more complex one)
        - apply Forward Checking to exit branch early: track all possible options for remaining unassigned choices, and backtrack search once a variable cannot be assigned
        - track Arc Consistency to exit branch early: one variable is Arc Consistent with another if there's a way to assign the latter without removing all options for former
        - exploit structure of CSPs by decomposing graph to subproblems (or independent problems if possible), can reduce branching factor, use optimized tree algorithms, etc.
- Iterative Algorithms: effective if there are either many or few solutions, however critical ratio #constraints/#variables cause issues