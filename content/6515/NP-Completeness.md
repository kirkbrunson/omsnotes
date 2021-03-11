
## NP1 - Definitions
- How to prove high degree of computationally intractable? Show it's NP-Complete
- Complexity Classes
	- NP = class of all search problems (can also define as decision problems). Search problem: problem where we can verify a solution in polynomial time (separate from solving)
	- P = class of search problems that are solvable in polynomial time. Thus P is a subset of NP (since can verify a problem in at worst same time it takes to solve it, i.e. with similar or same algorithm)
	- P = polynomial time, NP = nondeterministic polynomial time (nondeterministic means adding randomness)
- Search Problems
	- Form: Given instance I, find a solution S for I if one exists. Otherwise, output No if I has no solutions
	- Requirement: given an instance I and solution S, then we can verify that S is a solution to I in polynomial time (in |I|)
	- Examples covered previously: SAT (NP), Map coloring (NP), MST (P)
		- Knapsack is not necessarily P nor NP since it's not polynomial relative to the input size. However there is a variant of Knapsack with a goal value (call this Knacksack-Search) which is in P and NP (key difference: reaching at least g bounds the problem which can be solved with binary search on values)
- Proving problem is in P
	- Show it is a search problem
	- Show a solution can be found in poly-time
- Proving problem is in NP
	- Show a solution can be verified in poly-time
- What if P actually = NP?
	- P=NP being true would mean *generating* a solution would be as-hard-as *verifying* a solution (e.g. proof). Fortunately and unfortunately, this is not the case
- Intractable Problems: unlikely to be solved efficiently
- NP-Completeness
	- NP-complete problems are the hardest problems in class NP (in particular, P!=NP for NP-complete problems, so all NP-complete problems are not in P)
		- Interesting: if NP-complete problem can be solved in poly-time, then all problem in NP can be solved in poly-time (b/c problems can be reduced to an NP-complete problem)
	- Specifically, NP-complete means the following properties hold:
		- a) problem is in NP
		- b) if we can solve problem in poly-time, then we can solve every problem in NP in poly-time (i.e. other NP problems can be reduced to this problem)
- Reductions
	- A->B or A<=B (B is at least as computationally hard as A)
	- Treat algorithms as black-boxes, and define in-between functions f and h to fit the pipeline: input_to_A -> f -> B -> h -> output_to_A
- Proving NP-Completeness
	- If you can reduce known NP-complete problem to new NP problem, then you can prove NP-completeness for the new problem. E.g. use SAT (known NP-complete) and try to reduce it to new NP problem to check NP-complete
		- Make sure direction of reduction is correct, common mistake for students

## NP2 - 3 SAT
- 3SAT is NP-complete which can be proved by reducing to SAT problem
	- a) 3SAT is in NP
		- Can verify solutions in polynomial time (for m clauses, O(m) time to verify)
	- b) SAT -> 3SAT reduction
		- For SAT, can construct pipeline f->f'->3SAT->sigma'->sigma (or No instead of sigma'->sigma). Construct s.t. sigma' satisfies f' <=> sigma satisfies f
		- Construct input f' by reducing all clauses size >3 by introducing new set of variables and splitting into several equivalent clauses
			- For k clauses, create k-3 new variables and replace original clause by k-2 clauses in pattern: `C' = (a1 | a2 | y1) & (~y1 | a3 | y2) & (~y2 | a4 | y3) & ... & (~y_k-3 | a_k-1 | a_k)`

## NP3 - Graph Problems
- Going over NP-Complete Graph Problems: IS, Cliques, VC
- Independent Sets (IS): given undirected graph, find set of vertices which share no edges. Small IS is trivial, large IS is hard
	- NP-Complete variant: finding largest size IS with goal size g
	- Theorem: Max-IS is NP-Hard. NP-Hard means everything in NP can reduced to NP-hard, which means the problem is at least as hard as everything in NP (i.e. more general problem)
- Cliques: given undirected graph, find large cliques (fully-connected subgraphs). Small cliques are trivial to find, large is hard
	- NP-Complete variant: finding largest size clique S with goal size of g
	- NP-Complete variant: finding smallest size vertex cover set S with max budget size b


## NP4 - ~~Knapsack~~ Subset Sum
- 3SAT -> Subset-sum Reduction
	- Subset sum problem: given positive integers a1..an and target t, output subset S of {1..n} where sum_over_i{a_i} = t if such a subset exists (NO otherwise)
		- O(nt) algorithm using DP
	- Reduction proof
		- a) Can verify in O(nlog(t)) time, which is polynomial in input size
		- b) Convert SAT input to 2n+2m numbers (x and ~x for each variable and for each clause) + target t. All numbers are huge (<= n+m digits long and base 10). Use first n digits of each number to encode information about variable assignments, then next m corresponding to clause assignment. Add "buffer" numbers for the last m digits of s1..sn
			- Digits are organized to constrain solution to match SAT solution
- Subset-sum -> Knapsack (HW problem)

## NP5 - Halting Problem
- Undecidable problem = computationally impossible
	- No algorithm solves the problem on every input (even with unlimited time and space)
- Halting Problem (HP): given a problem P with an input I, return True if P(I) ever terminates and False if it has an infinite loop
- Prove HP is undecidable by contradiction: suppose there was an algorithm that solves HP on every input. If a solution T did exist, then passing a function (call it 'harmful') that loops if T is True and returns otherwise makes it such that harmful(harmful) leads to contradition in all cases, and thus cannot exist (therefore T doesn't exist)!
