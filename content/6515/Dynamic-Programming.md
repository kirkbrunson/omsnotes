## DP1: FIB-LIS-LCS

- Dynamic Programming (DP): key to mastery is a lot of practice (problems are similar in nature)
- DP reduces the number of unnecessary re-computations of subproblems by "building-up" to the final solution (as opposed to "recursing-down")
    - *No recursion*, however there are recursive formulas
    - Memoization: tracking which subproblems are solved using hash table or other structure (not used in this course)
- Steps to apply DP:
    1.  Define subproblem in words (try same problem on prefix of input)
    2.  State recursive relation (mathematical notation)
        - If cannot create recursive relation, then redefine subproblem
- FIB: Fibonnaci Number Calculation
- LIS: Longest Increasing Subsequence
- LCS: Longest Common Subsequence

## DP2: Knapsack-Chain Multiply

- DP can only be used if problem can be expressed as *optimal* subproblem solutions. Otherwise, can't build-up to final solution
- Note: to find polynomial efficiency in *input size*, need to calculate *input space* first, and efficiency is relative to the input space (e.g. for log(B) input space, B is exponential)
    - NP-Complete proof explains this (which is covered later in course)
- Knapsack Problem (several variants as well)
- Chain Matrix Multiplication

## DP3: Shortest Paths

- Inputs to consider:
    - Kind of graph (DG - directed graph or DAG - directed acyclic graph)
    - Negative weights?
    - Path or walk? (i.e. can revisit nodes?)
- Classical DAG shortest path: Directed graph with start/end node and positive edge weights, apply Dijkstra's alg (BFS using a minheap)
	- Use DP if the edge weights are negative
    - Negative weight cycles (cycle with weight sum < 0) causes path problem to be not well defined
- Bellman-Ford: solves shortest path for DG without negative weight cycles
	- To find negative weight cycles for DG: use same algorithm, and notice that i = n will be different from i = n-1
- Solving All-pairs shortest path for DG:
    - Easy sol'n: run Bellman-Ford for all s in V, Runtime is then O((n^2)v)
    - Different sol'n: Floyd-Warshall which is O(n^3)