
## LP1 - Linear Programming
- Max-flow via LP
	- m variables: f_e for each edge e
	- Objective function: `max sum_over_edges{f}`
	- Subject to: flow capacity + conservation of flow
- LP appoach: given variables, model objective function (max/min of linear combination of variables) subject to constraints (linear comb of variables)
	- Geometric view: plot constraints and find overlapping region
		- Feasible region is space where solution can exist. Find optima by looking at intersections with corners of feasible region
	- Non-negativity constraint: needs to be positive
- LP Key issues
	- Optimum may be non-integer (LP4 goes over ways to resolve)
	- LP is in P, however ILP is in NP (integer linear programming)
	- Vertex = Corner
	- Feasible region is convex. Thus, optimum solutions only lie on vertex
- LP Linear Algebra view: compress into matrices
- Converting to standard form:
	- Geq -> mult by -1 to get Leq
	- Eq -> split into Geq and Leq, then convert Leq to Geq
	- Missing non-negative constraint -> Split x into x+ and x-, apply non-neg constraints to x+ and x-, then set x = x+ - x-
- Finding vertices in feasible region:
	- Feasible region is a n-dimensional convex polyhedron
	- At most n+m choose n vertices (which can be huge), and each vertex can have nm neighbors
- LP Algorithms
	- Polynomial-time: ellipsoid algorithms & interior point methods
	- (worst-case) Exponential-time: Simplex algorithm
		- Widely used still b/c solution guaranteed optimal! Also fast on Huge LPs
- Simplex Algorithm idea
	- Given vertices, start at vertex 0 (at 0 coordinates). Look for neighboring vertices with higher objective value, if one is found move there, then repeat. If one can't be found, it's the optima -- return!

## LP2 - LP Geometry
- LP Optimum is achieved at vertices of feasible region EXCEPT if:
	- LP is infeasible (constraints don't have intersection, so feasible region is empty)
		- Depends on constraints only
	- LP is unbounded (no upper-bound -- optima is arbitrarily large)
		- Depends on objective function
- Determining if LP is infeasible:
	- Add variable z to constraint, then run LP with objective fn of max z. Intuition: since z is unbounded, it can be +/-inf so technically it makes any equation solvable (if it wasn't previously). If z>=0, that means it was previously solvable without z, otherwise if it's negative then without z it isn't regularly feasible.
		- If max z is negative, then problem is infeasible
		- Otherwise, it is feasible and we have starting point for simplex

## LP3 - Duality
- Interesting: objective function can be constructed from linear combination of constraints to get upper-bound!
	- Solving this can be constructed as a seperate LP (dual LP) based on coefficients in original (primal LP)!
- Dual LP general form (covered in lecture)
- Weak Duality Theorem:
	- Given feasible x for primal LP and feasible y for dual LP, `c^T x <= b^T y`
		- I.e. primal LP obj fn is upper-bounded by dual LP obj fn
	- Corollary: if we find feasible x and feasible y where `c^T x <= b^T y` holds, x and y are optimal
	- Corollary 2: if primal LP is unbounded, dual is infeasible. Likewise, if dual is unbounded, primal is infeasible.
		- Not IFF -- both can be infeasible. Direction matters
- Checking if primal LP is unbounded:
	- Check if primal is feasible. If true, then go to next step, otherwise it's infeasible
	- Check if dual is infeasible, then primal is either infeasible or unbounded. Since we know it's not infeasible, then it's unbounded
- Strong Duality Theorem:
	- Primal LP is feasible and bounded IFF dual LP is feasible and bounded
		- Feasible and bounded is equiv to having an optimal solution
	- Restated: primal has optimal x* IFF dual has optimal y* and `c^T x* = b^T y*`
		- Interesting tidbit: same formulation as max-flow min-cut

## LP4 - Max-SAT Approximation
- Max-SAT: Given regular SAT problem input (n variables, m clauses), output assignment *maximizing* # of satisfied clauses
	- Is NP-Hard (whereas SAT is NP-Complete)
- Approximate Max-SAT formulation
	- Goal to approximate m* <= m representing # of satisfiable clauses
	- Measure success by lower-bound guarantees -- for example, if solution l>=m/2, then this is 1/2-approximation
- Simple Algorithm: 1/2 approximation algorithm (and 1-2^-k for Ek-SAT)
	- Approach: Use conditional expectation. For each var i=1..n, calculate expectation of setting x_i=T or x_i=F and take better
		- Expectation of setting each variable to T/F randomly is m/2, the above approach guarantees the lower-bound
	- Special-case for exactly-k-SAT: (1-2^-k)-approx. 7/8-approx for exactly-3-SAT is current best, proven NP-hard to do better
- LP-based approximation using Integer Programming: (1-1/e) approx algorithm
	- ILP: a LP problem where variable is constrained to be an integer
		- Intuition: adding a "grid" to plot, and only looking for points in feasible region AND on grid
		- ILP is NP-hard. Can reduce Max-SAT->ILP
	- Approach: solve regular LP, then find nearest integer point as feasible soln to ILP (not guaranteed optimal though good approximate)
		- From LP solution, round each variable to 1 with probability=the value from LP (note it's already constrained between 0,1)
		- Get expected value of the LP using Arithmetic-Geomtric mean (AM-GM) inequality, then some algebra+calculus+Taylor series black magic
	- Can often simplify NP-hard problem to ILP, then get approximatation!
- Best of 2: 3/4 approx algorithm
	- Interesting: k=1 LP performs better, tie for k=2, k=3 simple performs better, then follows generalized equations (1-2^-k vs. 1-(1-1/k)^k). Always at least 3/4-approximation!
