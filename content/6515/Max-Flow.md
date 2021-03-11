
## MF1 - Ford Fulkerson Algorithm
- Flow Networks
	- Idea: sending "supply" (e.g. internet traffic) from source vertex -> target vertex (labeled vertices s->t)
	- Given flow network (positive-weight directed graph G=(V,E)) where each weight is a "capacity", find all flows f_e for e in E the following flow properties apply:
		- Capacity constraint: 0 <= flow through edge <= max capacity
		- Conservation of flow: for all vertices besides s and t, flow into vertex = flow out of vertex
- Residual Network
	- Given flow network G and current flow f, G^f=(V, E^f) where E^f is E with updated edge capacities c_vw-f_vw AND additional reversed-direction edges of capacity f_vw
- Ford-Fulkerson Algorithm
	```text
	1) Init f_e = 0 for all edges
	2) Build residual network G^f given current flow f (O(n) time since n-1 edges, and check each edge)
	3) Check for s->t path P in G^f (O(n+m) time -> O(m) since DFS/BFS). If no path, then just return f (algorithm is finished!)
	4) Given P, find/track c(P) which is min capacity along P in G^f
	5) Augment f by c(P) units along P (O(n) since n-1 edges again). "Augment" means increase non-full forward edges, and decrease non-empty backward edges. Forward edges are original graph, backwards were added from residual
	6) Repeat from 2) until no s->t path in residual
	
	Running time: O(mC) given all capacities are integers (big assumption!) and using C=max capacity along path, m=number of edges
	```
- Max-Flow Alternate: Edmonds-Karp which is O(m^2 * n)

## MF2 - Max-Flow Min-Cut
- Proving correctness for:
	- 1) max-flow min-cut theorem
	- 2) Ford-Fulkerson (max flow via iteratively adding residual graph flow)
- Min-cut problem
	- General Problem: Given flow network, find min-capacity cut (i.e., L and R partitions)
		- cut definition: Given any two partitions of vertices L and R (doesn't need to be connected), let st-cut define the set of edges that go from node in L to node in R
		- st-cut definition (`st-cut`): a cut, but specified vertices `s` and `t` cannot be in same set
		- capacity definition (`capacity(L,R)`): given set of edges between two sets of vertices L and R, sum of flow capacity for those edges
- Max-flow = Min st-cut Theorem
	- Theorem: size of max-flow = min capacity of st-cut
	- Review proof in lecture

## MF3 - Image Segmentation
- Goal: separate an image into objects (e.g. foreground vs background)
- Problem formulation: View image as an undirected graph, and partition into max-score sets, then find max flow of flow network
	- Formulating as a graph
		- `G=(V,E)`: `V` is each pixel, `E` connects to all neighboring pixels
		- For each node `n` in `V`, define `f_i` as likelihood/weight of pixel being in foreground and `b_i` for likelihood/weight of pixel in background, both non-negative
		- For each edge from node `i` to `j`, assign penalty `p_ij` to put `i` and `j` into different sets (to "separate"). Intuitive example: `p_ij=1` if same color, else `p_ij=0` (since want to separate sets)
	- Partitioning into max-score sets
		- Can define weight of two partitions `F`,`B` as `w(F,B) = sum_over_F{f_i} + sum_over_B{b_j} - sum_over_cut_edges{p_ij}`
	- Reduce problem to min st-cut problem
		- Redefine as minimization problem so all terms are positive (otherwise, can't model as st-cut problem). Given image, calculate `L = sum_over_V{f_i + b_i}`, then formulate problem as `w'(F,B) = L - w(F,B) = sum_over_F{b_i} + sum_over_B{f_j} + sum_over_cut_edges{p_ij}`
	- Formulate flow network
		- Make each pixel a node, add Bidirectional edges between neighbors (up-down-left-right)
		- Add two extra nodes `s`, `t`. Let `s` correspond to foreground, `t` to background. Add edges from `s->i` of value `f_i` and edges `i->t` of value `b_i`
	- Get max-flow as min s-t cut, since by definition, `capacity(F,B) = w'(F,B)`. So from that, we have `F` and `B`
- Summarization: given input, define flow network, get max flow which is equiv to capacity of min s-t cut, which is equivalent to maximizing score

## MF4 - Edmonds-Karp Algorithm
- Edmonds-Karp Overview
	- Finds augmenting paths using BFS in O(nm^2) time, with no constraints on flow capacities (just need to be positive)!
		- This is better than Ford-Fulkerson which is pseudo-polynomial b/c O(mC) with C=size of max flow, and requires that capacities are integers
	- Ford-Fulkerson with BFS (as opposed to BFS or DFS)
- Lemma for BFS w/ Max-Flow: For edge e, in G^f, e is deleted + inserted <= n/2 times
	- Prove by abstracting property of BFS

## MF5 - Max Flow Generalization
- Max flow with demand `d(e)` for each edge (i.e. desired amount of flow through edge)
	- "Feasible" flow is a flow f where `d(e) <= f(e) <= c(e)` for all edges `e`
- Reducing Feasible flow problem -> Max flow problem
	- Given Feasible flow inputs, transform directed graph `G` to `G'` where edge weights from original graph are `c(e) - d(e)` AND `s'` has edges to all vertices s.t. `c(s'->v) = sum_over_incoming_edges_to_v{d(e}` AND `t'` has edges to all vertices s.t. `c(v->t') = sum_over_outgoing_edges_from_v{d(e}` AND `c(t->s)=+inf` (no constraints)
		- Added edges maintain conservation of flow
- Applying max-flow algorithm to `G'`
	- Lemma: G has feasible flow == G' has a saturating flow (s' and t' full capacity)
		- Prove by showing saturating flow in G', call it f', can be reduced to valid and feasible flows in f (also prove reverse fact)
- Finishing up: run Max flow, then convert Max flow output -> Feasible flow output
	- Run max flow, then check if G' has a saturating flow. If yes, then start at the feasible flow `f` and augment in residual graph `G^f` where residual backward edges are `f(e)-d(e)` instead of just `f(e)`
