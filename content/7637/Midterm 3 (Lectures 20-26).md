# Midterm 3 (Lesson 20-26)

**Note**: these notes were taken in the Summer 20' semester and aim to accompany (i.e. not replace) the lecture material. Make sure to review the current semester's deadlines/material for the precise course details to follow!

## 20 - Constraint Propagation
- Constraint Propagation:
	- Given characterized problem and necessary conditions (constraints), constraint propagation is a method of assigning values to problem that satisfies constraints.
- Image Processing:
	- Image processing example: pixels translate to 3D images using subtasks (converting to lines, identifying surfaces and orientations, then 3D rendering). Constraints in this case are defined junctions (e.g. Y-, W-, L-, T-junctions) and edges (fold, blade)
- NLP:
	- NLP Example: when processing language, structure of sentence has constraints (e.g. noun/verb phrase distinction), as well as different words (like prepositions)
- Advanced Problems:
	- More complex ontology needed for more complex case

## 21 - Configuration
- Design and Configuration:
	- Design: taking needs, and creating output to address needs. Often both problems and design co-evolve as design progresses
	- Configuration is when design structure is already known, however values need to be assigned to satisfy constraints
    - Real-life example, cooking! Creating new recipe vs. cooking and adjusting existing    
- Plan Refinement:
	- Since problem evolves with design, need to iteratively reassess needs and constraints to fit needs
- Connections to earlier topics:
	- Classification makes sense of world, configuration creates world
    - Case-based reasoning starts with template then tweaks, whereas configuration starts with constraints and assigns
    - Planning is used to start configuration
    - (future topic) Interesting: configuration can be understood as diagnosis in reverse
    
## 22 - Diagnosis
- Diagnosis:
	- Given parameter values, what classification is it?
    - Alternatively: determining what is wrong with a malfunctioning device
- Data and hypothesis spaces:
	- Goal: find hypothesis that maps to data for a given case
    - Abstract in data space to find hypothesis
    - Refine in hypothesis to check application in data space
- Criteria for choosing hypothesis:
    - 1.  Hypothesis must cover as much data as possible
    - 2.  Should use smallest amount of hypothesis (parsimony)
    - 3.  Given hypothesis have different likelihoods, try to use most likely hypothesis
- Of course, after understanding hypothesis then go ahead with fixing the problem!
- Mapping data (D) to hypothesis (H):
	- Problems with H->D many-to-one mapping, and one-to-many H->D mappings
    - Additionally, conflicts with data constraints, e.g. cancellation effects
- Two views of diagnosis (deduction vs abduction):
	- Let Rule = Cause->Effect. 
	- Deduction=given rule and cause, deduce effect. 
	- Induction=given cause and effect, induce rule. 
	- Abduction=given rule and effect, abduce a cause. 
- Interesting: only deduction is truth-preserving
    
## 23 - Learning by Correcting Mistakes
- Explanation-based learning revisited:
	- Initial agent explanation is often a good starting point. However, what happens when given contradicting examples, how to learn from mistakes?
- Isolating mistakes:
	- Goal: find distinct features that characterize positive and negative examples.
    - Can visualize by mapping features and looking at disjunction space of features, where intersection space is cup-defining features. In case of mistake, analyze features in disjunction
- Explaining mistakes:
	- Can technically fix after isolating model, though missing explanation which skips learning
    - "Explanation" can be understood as object hierarchy model (e.g. from explanation-based learning lesson)
- Correcting mistakes:
	- "Correct" by updating model accordingly and with justification. Design choice here has impact on other decisions, so need to choose wisely

## 24 - Meta-Reasoning
- Mistakes in knowledge, reasoning, and learning:
	- Mistake in reasoning example: block world, how applying sound reasoning can lead to dead-end states that need to be reverted
    - Mistake in knowledge/learning example: explanation-based learning example with cup handle, adjusting knowledge model to better fit real-world scenario (by not excluding cases)
- Gaps in knowledge and reasoning:
	- While approach might be valid, base knowledge not guaranteed to be complete
    - If agent is able to detect knowledge gap, can design agent to set learning goals to fill gaps!
- Strategy selection and integration:
	- Part of Metacognition -> Selecting different strategies given current knowledge to solve problems. e.g.: Case-Based reasoning, Constraint Propagation, Generate & Test, Means-End Analysis, Analogical Reasoning, Planning, Problem Reduction
    - Also part of Metacoginition -> Selecting learning strategies, e.g.: Learning by Recording Cases, Classification, Explanation-Based Learning, Incremental Concept Learning, Version Spaces
    - Strategy integration can be iterative -- if initial strategy doesn't work, learn and adapt according
- Meta-meta-reasoning:
	- Strategies used as part of meta-reasoning can also be used to implement meta-reasoning framework
    - Better to think about meta-reasoning as a single layer, otherwise infinite nesting!
- Goal-based autonomy:
	- Depending on the goal (e.g. completeness, efficiency, speed, etc.) and feedback, meta-reasoning allows agent infer what to do for new goals and adjust their approach accordingly

## 25 - Advanced Topics
- Visuospatial reasoning: can be understood as part of constraint propagation (recall 2D edge detection example). Visuospatial knowledge from images is at most implicit (need to make inferences for knowledge, e.g. photo of cup with pool of water around it)
- Two views of reasoning: propositional (e.g. verbal encoding) vs. analogical (visuospatial encoding). Human-like thinking more analogical, uses mental imagery (currently figuring-out how to do with computers)
- Systems Thinking: discretely reasoning about multi-component systems (across all levels of abstraction, even "invisible" relationships)
- One way of modeling a system: Structure-Behavior-Function
- Design Thinking: reasoning and formulating unconstrained, open problems in the world - Creativity: can be understood as process of producing non-obvious (unexpected), novel, desirable product. Personal definition: using available resources in novel combinations as part to create novel products    
- Some other properties of creativity: emergence, re-representation, serendipity
- AI Ethics: are we doing the "right" kind of things with AI? How does this affect the economy, military, AI-rights, etc.?
    
## 26 - Wrap-Up
- Cognitive structures revisited: metacognition, deliberation, and reaction spaces ("thinking" in see-think-act model of an agent)
- Review of KBAI domains:
    - Fundamentals (Generate & Test, Means-End, Production Systems),
    - Planning (using formal logic),
    - Common Sense Reasoning (expectations, scripts),
    - Learning (Recording Cases, Incremental Concept Learning, Classification, Version Spaces),
    - Analogical Reasoning (Recording Cases, Explanation-based Learning, Reasoning),
    - Visuospatial Reasoning (constraint propogation),
    - Design & Creativity (building from configuration, diagnosis),
    - Metacognition (correcting mistakes, meta-reasoning, ethics)
- Review of principles explored in course:
    - Principle #1: KBAI agents represent and organize knowledge into knowledge structures to guide and support reasoning
    - Principle #2: Learning in KBAI is often incremental
    - Principle #3: Reasoning is top-down as well as bottom-up
    - Principle #4: KBAI agents match methods to tasks
    - Principle #5: KBAI agents use heuristics to find solutions that are good enough, though not necessarily optimal
    - Principle #6: KBAI agents make use of recurring patterns in solving problems
    - Principle #7: The architecture of KBAI agents enables reasoning, learning, and memory to support and constrain each other
- Current Research includes many existing projects, including Dramatis, DANE, Cyc and OMCS, VITA, CALO, and Wolfram Alpha! :O