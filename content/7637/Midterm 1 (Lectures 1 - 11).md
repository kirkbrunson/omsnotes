# Midterm 1 (Lectures 1-11)

**Note**: these notes were taken in the Summer 20' semester and aim to accompany (i.e. not replace) the lecture material. Make sure to review the current semester's deadlines/material for the precise course details to follow!

## 1 - Intro to KBAI
- The 4 "quadrants" of AI are introduced as a model for understanding different types of AI.
- AI can broadly be applied to any problem, though the approach taken can vary drastically.
- Cognitive Systems focus on systems emulating human-like thinking, a sort-of subset of KBAI.
- A cognition I/O diagram is presented (balancing reaction, reasoning, and meta-cognition).
    

## 2 - Intro to CS7637
- This course will be using active learning -- iterative examples, exercises, and projects to build strong foundational knowledge!
- The structure and how to solve Raven's matrices. These examples illustrate the different subtle methods and ways of thinking needed to solve problems intuitive for humans. Additionally, a "correct" answer is sometimes open to interpretation.
- The main principles of this class center around understanding how KBAI agents iteratively learn, reason, and execute to solve problems given constraints and heuristics.
    

## 3 - Semantic Networks
- Loosely defined: Knowledge = Language + Content.
- Knowledge representation is the process of discretely defining a knowledge state.
- Semantic Networks are a type of knowledge representation used to demonstrate relationships and state transitions.
- Generally good knowledge representations define relationships effectively with minimal waste so that they can be computed.
- A helpful "guards and prisoners" example is presented. One approach to solving problems is identifying all states, and then eliminating invalid or repeated ones.
- Problem solving becomes much easier with the correct knowledge representation.
- When multiple choices are possible, a heuristic needs to be defined to pick the best option (similarity weights is one example).
- For KBAI, there is both a focus on objects AND the relationships between objects in a given state.
    

## 4 - Generate & Test
- KBAI can be abstracted to 3 components: Knowledge Representation, Problem Solving Techniques, and Architecture. 
	- Generate & Test is a type of Problem Solving Technique.
- Smart generators/testers eliminate unnecessary complexity which dumb systems can cause.
- How much responsibility split between the generator/tester is a design choice.    
- Generate & test is valuable in the absence of: complete & correct knowledge of the world, infinite computational resources, and consistent reasoning.
    

## 5 - Means-End Analysis
- State Spaces involve representing different problem states with a well-formed structure (e.g. nodes and paths). Goal is to make the problem more well-formed.
- Pros/Cons of Means-End Analysis
	- Pros: can be generally applied to a lot of problems, especially useful & fundamental for well-formed problems
    - Cons: can be computationally heavy, and no guarantee for an answer (algorithm can get stuck)
- Problem Reduction is used to reduce harder problems into easier sub-problems/sub-goals. These goals can be used in heuristic calculation to optimally solve the problem.
- Means-End Analysis can be applied to Raven's matrices by: defining the set of operations, generating possible states using the operations, and respecting the semantic relationships between rows/columns. 
	- Implementation-wise this can be achieved by defining goals+sub-goals to solve.
- Means-End analysis is a "weak" method that doesn't use that much knowledge of the domain, "stronger" methods can be applied with more domain knowledge (learned later in the course).
    

## 6 - Production Systems
- A production system is a cognitive architecture with rules to represent knowledge. It is an example of procedural learning (using SOAR model).
- `f(P*) -> A` is a common model for representing goal of agents to map percepts to actions. 
	- When a good architecture is held constant, this can map content to behavior (end-goal: action selection!)
- Knowledge can be represented as a set of features/values that define a state.
- Production rules define decisions based on knowledge and goals of a system.
- Procedural knowledge represents the "how" to do something.
	- Conflicts of procedural knowledge ("impasses") can be overcome by learning new rules from previous episodic knowledge ("chunking").
- Developing a unified theory of learning requires understanding "what" and "why" we learn (examples and motivation), and then the "how" (implementation).    

## 7 - Frames
- Frames are a type of knowledge representation (structure: JSON-like) that are used to capture contextual information of a property.
- Common Sense reasoning requires inference. Contextual information from frames can help correctness of inferences.
- 3 properties of frames: 
	- 1) they represent stereotypes (common properties), 
	- 2) they can use defaults, 
	- 3) they exhibit inheritance (class/instance framework)
- Semantic networks can be represented as frames. The structure of the frame (i.e. slots) depends on long-term background knowledge (inferences made here).
- Challenge with advanced situations like stories: how to capture the contextual information effectively?
    

## 8 - Learning by Recording Cases
- Learning by recording cases applies memory to solve new problems by comparing them to previously seen problems.
- Nearest neighbor can be applied to select the closest solution from memory by representing knowledge as values/coordinates. When generalized to K-dimensional space, this is KNN.
- KNN is limited by accuracy in complex/high-dimensional spaces and non-quantitative data (e.g. qualitative, descriptive labels).

## 9 - Case-based Reasoning
- Learning by Recording Cases is using memory to solve identical problem structures, while Case-based reasoning is solving similar problem structures by reasoning from memory.
- 4 Steps for Case-based reasoning: 
	- 1) Retrieval,
	- 2) Adaptation,
	- 3) Evaluation,
	- 4) Storage of new case into memory.
	- These can be performed non-linearly to better fit computation
- Assumptions for Case-based reasoning: patterns exists in the world, and similar problems correspond to similar solutions.
- Previous problems can be adapted recursively (reconstructed using old problems), using heuristics to guide reasoning, and by expanding/building on old solutions.
- Storage should aim to be information-rich and easy to search.
	- A discrimination tree is a type of binary tree which can achieve this.
- Storing failed cases can add value to reasoning as well (especially more interesting cases). Priority should go to more interesting cases to store into memory.

## 10 - Incremental Concept Learning
- Incremental concept learning involves: 
	- 1) Variabilzation (defining baseline definition of concepts)
	- 2) Generalization or Specialization based on examples.
- Given example, if it is positive example and doesn't fit model, then need to generalize. Otherwise, if it is a negative example and does fit model, then need to specialize. Other cases no action is needed.
- A concept/model can be represented as a semantic network (nodes/edges).
- Different heuristics for updating the concept/model based on provided examples are: 
	- drop-link (edge not necessary), 
	- require-link (edge necessary), 
	- forbid-link (edge needs to be absent), 
	- enlarge-set (expand node definition), 
	- climb-tree (generalize nodes based on background knowledge), 
	- close-interval (generalize quantitative measure by expanding range).
    

## 11 - Classification
- Classification is needed to significantly reduce the mapping space of percepts -> actions.
	- By creating different concepts as bridge mappings (equivalence classes), the mapping problem is reduced significantly!
- Concept Hierarchies involve object-orient programming (OOP)-like class organization structure with inheritance, classes, subclasses, superclasses, etc.
- Concepts that can be defined formally are significantly easier to compute (more objective). 
- Three types of concept categories (differing in formality) include: 
	- 1) Axiomatic (can be mathematically defined),
	- 2) Prototypical (base class with defaults), 
	- 3) Exemplar (most subjective, defined by implicit abstractions/examples)
- Top-down search is useful for answering specific queries. Bottom-up search is useful for building knowledge.