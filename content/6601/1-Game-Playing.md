
## 1 - Game Playing

- Main topics: Adversarial search, Minimax algorithm, Alpha-Beta pruning, Evaluation functions, Isolation Game Player, Multi-Player & Probabilistic Games
- Example of "Isolation" game + building game tree of outcomes, then backpropagating knowledge using minimax
    - Minimax algorithm: when propagating up, for each max node (you) pick max, for each min node (opponent) pick min
    - number of moves theoretically ~b^d for branching factor b and depth d
- "Do the simple thing first and only add intelligence when necessary"
- Depth limited search: only searching as far as you think you need for coverage (can use maths to get better approximations). Saves computation, though can lead to significantly different results
    - "Quiescent Search": applying search until find point where no more decision changes happen
- Iterative Deepening: apply quiescent search continuously until time runs out, and keep best answer at each level
    - Problem: "Horizon Effect", computer cannot see meta patterns
- Alpha-beta pruning: evaluate left-to-right, and prune child branches that aren't able to beat parent choice from already calculated sibling branches
    - Can re-evaluate order of nodes (since default left-to-right calculating) to increase pruning
    - Prune if subtree is going to be subsumed by a player decision anyways
- Interesting "Isolation"-specific insights (for vanilla version):
    - Can use symmetric properties of board to reduce computations in beginning
    - Outcome of game is known as soon as partition is found
- Multiplayer games: MAXN instead of Minimax (i.e. each level, player picks the best score for themselves)
    - Alpha-beta pruning still works as long as upper- and lower-bound exists for score. However, only shallow/immediate pruning, e.g. if max score is already reached or limits converge (no deep pruning)
- With probabilities involed, can still generate tree. Extra edges with given probabilities, scoring called EXPECTIMAX