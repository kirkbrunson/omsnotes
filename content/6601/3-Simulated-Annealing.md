
## 3 - Simulated Annealing (Iterative Improvement)

- "Do the stupid thing first, and only add intelligence when necessary". Then, adding a little intelligence and iteratively improving can get close-to-optimal solutional
    - "Do the stupid thing first, then add intelligence to solve the problem"
- n-Queens
    - Heuristic function: reduce number of attacks
    - Can be represented as hill-climbing problem
- Hill Climbing
    - Step towards direction of positive gradient. Can start large and get smaller if no convergence
    - Distinction between global and local maxima
    - Random restart idea: simulate start boards and simple heuristic, try to get to global maxima on at least 1 attempt. Don't research start spots that have been tried ("taboo" spots)
- Simulated Annealing
    
    - Background: regular annealing controls input to exploits natural tendency for structures to reach lowest-energy forms (e.g. mud cracks)
    - Algorithm:
    
    ```
    // Simulated Annealing Pseudocode
    // current and next are spots on graph T vs. E
    for t = 1 to inf:
        T <- schedule(t) // changes T from large to small
        if T=0 then return current
        next <- randomly selected successor of current
        if delta_E>0 then current <- next // if score improves, take it
        else current <- next with probaility e^(delta_E/T) // small T is regular hill climbing
    ```
    - Guaranteed global maxima given enough iterations and starting at high T
- Local Beam Search - run multiple simulated annealing particles at once, and compare generated neighbors. Keep the best one. Add randomness to make it stochastic and help prevent getting stuck in local maxima
- Genetic Algorithms
    - Define a "fitness" score, then select parents to "breed" set of generated states with skewed probability towards more fit boards (new bred states are some "cross-over" of parents)
    - Add random "mutations" on state to prevent non-convergence
    - Surprising, we can reasonably expect this to converge to optimal answer! (eventually, with enough generations)
    - Papers on Genetic algorithms report number of generations, strategies to optimize parameters for convergence, etc.
    - Often "the second-best solution to any problem"