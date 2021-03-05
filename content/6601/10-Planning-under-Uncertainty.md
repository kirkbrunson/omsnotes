## 10 - Planning under Uncertainty
- Intersection of Planning, Uncertainty, and Learning not yet explored in class
	- Markov Decision Processes (MDP) and partially observable MDP (POMDP) explore Planning and Uncertainty intersection. Reinforcement Learning (RL) intersects all 3
- MDPs are relevant when problem is stochastic in nature. Define state values to guide results
	- Define "policies" as the plan to guide towards best actions. Helps solve problems with large branching factors, large depth, and revisited states
		- Set goal to maximize the reward, set a "discount factor" to prioritize current states
		- Can understand value as the summed expectation of a policy path
		- Value iteration: process of defining value estimates for each state until convergence (based on given rewards of terminal states R(s)). Note you take the best action out of all possible actions to estimate value, starting from terminal node
- POMDPs are more generic, warrents more depth (e.g. separate classes), intuition is there are moves that are necessary to gain information however go against heuristic, and need to account for those somehow
	- One approach: do this by re-representing belief states to account for uncertainty