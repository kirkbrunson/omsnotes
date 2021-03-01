# Midterm 2 (Lectures 12-19)

**Note**: these notes were taken in the Summer 20' semester and aim to accompany (i.e. not replace) the lecture material. Make sure to review the current semester's deadlines/material for the precise course details to follow!

## 12 - Logic
- For inference, want AI agents to have/express statements of soundness (only valid conclusions can be proven) and completeness (if valid, it can be proven).
- A predicate maps object arguments to T/F values. Can use logical operators and qualifiers to create truth sentences/tables.
- Some operators logical statements: `&&`, `||`, `~`, `=>`(for a=>b, re-written ~a || b).
- Some features of some operators: Commutative, associative, distributive, de Morgan's law.
- Rules of Inference can be used to prove statements, e.g. Modus Ponens and Modus tollens.
- Proof by negation can prove a statement by assuming the inverse of the statement, and demonstrating it's impossible for that to be the case. 
	- Set-up w/ conjunctive normal form.
	- Formal structure is powerful for computation as it gives agent area to focus, e.g. horn clause.
- Cognitive connection: interestingly while inductive logic tied deeply w/ cognition, humans are generally more deductive than inductive, so small gap of behavior (will be addressed more later).
    
## 13 - Planning
- States, goals, and operators:
	- Formalize problem using propositional logic. Preconditions (define as positive) and postconditions specify criteria for applying operators for given states. Re-use criteria when possible to minimize parameters to track (i.e. as negations).
    - Note: planning deals with action selection through cognitive analysis. Not the same as search (i.e. traversing through all possibilities). Trading completeness for focus.
- Conflicts in planning:
	- "Linear planner" does not care about order, can lead to conflicts. Conflicts can be understood as inconsistencies between pre- and postconditions, i.e. one state causing a "blocker" for another's precondition.
- Partial-order planning:
	- Add Goal heuristic to define order of operators, and separate Goals in given problem. For each goal, create a separate plan, then merge in order of least conflicts. 
	- Solve conflicts by checking preconditions in partial plan with states in other plans. Prioritize goal that leads to more conflicts when creating plan.
	- Can be understood as a group of sub-agents working together ("society of mind").
- Hierarchical task networks:
	- Can abstract groups of common operators into a "macro"-operator -- i.e. generalizes well. To reason with multiple abstractions, need knowledge at multiple abstractions.

## 14 - Understanding
- (Understanding --> in the context of stories)
- Thematic Role Systems
	- KBAI approach: Semantic analysis at forefront (syntactic and lexical supporting). Define sentence as frames and semantic mapping. Allows for explicit definitions of inferences and expectations of inferences (thematic role frames)
    - Words assignment based on Constraints, e.g. prepositional constraints table (mapping preposition to allowed thematic roles)
- Ambiguity
	- Multiple works have different meanings in different contexts, how to resolve consistently? Use existing knowledge of world (formally defined structures like classification hierarchies), constraints, etc.
    - Puns introduce challenge since word can have multiple meanings and both make sense (e.g. "wondering why the ball got bigger, then it hit me!")
    - Can assign interpretation frame ("take" frame) with a defined structure + requirements based on knowledge. Can eliminate possible definitions using different contextual pieces from sentence/story
- Constraints
	- Based on prior knowledge, e.g. prepositional constraints table

## 15 - Commonsense Reasoning
- (in this scope: achieved using frame-based representation, left-to-right parsing)
- Problem: how to effectively capture contextual definitions and meanings in stories?
- Primitive Actions
	- A set of 14 atomic actions to formally contextualize stories. List for this class: move-body-part, move-object, expel, ingest, propel, speak, see, hear, smell, feel, move-possession, move-concept, think-about, conclude
    - Once ontology is defined, can apply when parsing through sentences. Can re-use frames defined in primitive actions, and use self-defined rules to populate the frames
- Actions and subactions
	- Implied actions: if primitive actions fail, AI agent needs to infer and figure-out what (if any) implied actions are present to properly contextualize the sentence
    - Sub-actions: contextual "story" information for primitive actions (e.g. move-object might necessitate an instance of move-body-part)
    - For multiple verbs in sentence, Action frames can contain other Action frames (however, this is not the same concept as sub-actions)  
- State changes
	- Frame can be used to represent state change that is a result of an action frame
    - When primitive action isn't clear, can encode as "do"-ing a state change
    
## 16 - Scripts
- Stories often require a lot of inference and commonsense reasoning. Scripts provide structure to derive this information by setting expectations about scenes in the world
- Defining Scripts
	- Script: a causally coherent set of events. Needs to make sense, and order matters. Represented as an ordered-set of frames
	- Parts of a script: entry conditions, expected results, props (objects), roles (agents), track (variations/"subclasses" of the script), scenes (sequence of events)
- Form vs. content
	- Given form of script (abstract), fill with content (instance)
- Generating Expectations
	- Expectation can be understood as adherence to a script. Opposite is surprise -- deviation from a script
- Hierarchy of Scripts
	- Concept of script sub-classes => Tracks (e.g. a generic restaurant script may have Coffeehouse, Fast Food, Formal Dining tracks, etc.)
    - Note: scripts focus on solution execution -- different from problem-solving KBAI methods (e.g. Generate&Test, Means-End Analysis)
    
## 17 - Explanation-Based Learning
- Explanation-based learning: focus on building new connections among existing concepts. Non-trivial problem for AI agents, requires creative thinking
- Concept Space
	- Can represent prior-knowledge used a graph-edge structure, maps objects (nodes), relationships (edges), and causal relationships between relationships (pointer between edges)
- Abstraction
	- Combine properties into more abstract levels (e.g. is liftable == is light and has handle). AI agent can learn abstractions from these base relationships
- Analogical knowledge transfer
	- Use examples from memory to create/justify abstractions and generate new conclusions
    - Importance of causal relationships: AI agent needs to be able to justify reasoning
    - During process, AI agent deliberately searches for information to justify/prove a statement ("what do I need to solve the problem?")
    
## 18 - Analogical Reasoning
- Analogical Reasoning: using knowledge from previous problems to understand/gain knowledge for new problems
- Similarity and case-based reasoning
	- Previous example of case-based learning using KNN (measure distance between old + new case) and discrimination tree (binary decision tree). Works for problems within domain, what about different domain? A: Analogical Reasoning! (find cross-domain analogies)
    - Need for cross-domain analogy examples: patient-tumor-laser, king-army-landmine. Can solve problem by overcoming constraints with novel approach!
- 4-dimensional Similarity matrix (Q's in-order): Relations? -> Objects? -> Features? -> Values?
	- All 4 Yes (in similary matrix) => Recording cases
	- 2/3 Yes => Case-based reasoning
	- Only relations => Analogical reasoning
- Process of analogical reasoning
	- General 5-step process: Retrieval -> Mapping -> Transfer -> Evaluation -> Storage
	- Compare with 4-step case-based reasoning: Retrieval -> Adaptation -> Evaluation -> Storage
- 1.  Retrieval: based on similarity
	- Use both superficial similarity (features, counts, objects) and deep similarity (relationships between objects and other relationships). Distinction between binary and tertiary+ relationships
	- 3 Types of similarity: Semantic (conceptual), Pragmatic (external factors, e.g. goals), Structural (representations, e.g. features)
- 2.  Analogical mapping: figure-out what in target maps to what in source cases. Representing objects as goals/relationships can better formulate problem
	- Highly dependent on having the right model (can make-or-break mappings)
- 3.  Transfer: use abstracted solution from source and apply to target
- 4.  Evaluation + 5) Storage: self-explanatory, same as-in case-based learning
- Design by analogy
- Applying similar concepts across domains to design, e.g. design inspired by biology! Use similar models to build suggestions as design progresses

## 19 - Version Spaces
- Definitions
	- Recall incremental concept learning: based on examples specializes or generalizes, good for in-order, teacher needs to be available
    - Version space: technique for applying incremental concept learning -- keep two models (general + specific)
- Abstract Version spaces
	- Specific and general models trained concurrently. Start at both extremes: then specific gets generalized by positive examples, general gets specialized by negative examples. When applying new examples, adjust model in a way that previous examples are still true
    - When models converge, have trained model. Convergence is important -- otherwise won't reach conclusion. With reasonable about of examples can expect convergence
- Algorithm for Version spaces
	- For each example:
		- If positive example, generalize specific models to fit, prune general models that do not
		- If negative example, specialize general models to fit, prune specific examples
		- Prune subsumed models (models covered by more developed models)
- Identification Trees
	- Type of binary tree developed w/ all data upfront (decision tree learning). Effective but non-trivially hard as dataset + features increase
    - Better than incremental learning since not impacted by data order. However, need all data upfront