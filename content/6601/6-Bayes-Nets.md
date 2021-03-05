## 6 - Bayes Nets

- Simple Bayes Networks
    
    - Base problem: Have event A->B, where only B is observable. Also have P(A), P(B|A) and P(B|not A). Have causal reasoning, but want inverse diagnostic reasoning P(A|B) and P(A|not b)
        - E.g.: Having cancer `A`, leads to test result `B`. `A` is not observable and is 'hidden', so we need to infer based on test results
    - Computing Bayes Rule: interesting transform to use own normalizer
        - Since `P(A|B) + P(not A|B) = 1`, can rewrite Bayes rule `P(A|B)` and `P(not A|B)` as:
            - `P(A|B) = eta * P'(A|B)` and `P(not A|B) = eta * P'(not A|B)` where `P'(A|B) = P(B|A) * P(A)`, `P'(not A|B) = P(B|not A) * P(not A)` and `eta = (P'(A|B) + P'(not A|B))^-1`
        - Significant since `P(B)` may be nontrivial to calculate/define (e.g. postivive test result in general), and this lets us just use the information in the numerator to solve!
            - Use this method when solving multiplicative terms, e.g. probability given two independent test results
- Conditional Independence (treated very differently from Absolute Independence)
    
    - Given hidden cause `A` leading to events `B` and `C`, `B` is conditionally independent from `C` if `P(C|A, B) = P(C|A)`
        - Intuitively, information on one does not influence the other GIVEN that `A` is known (hence, it's conditionally independent, and not just independent)
        - If `A` is NOT known, then the result from `B` will actually influence the probability of `C`, since probabilities are determined using known information!
    - When solving problems, expand all terms normally, then reduce using conditional independence information
- Explaining Away
    
    - Types of cause/event relationships
        - Single cause leads to multiple events (cancer tests example)
        - Multiple causes lead to single event
    - "Explaining away": for event with multiple causes, if event outcome and one cause is known, that information "explains away" the other, e.g. gives information that shifts likelihood of other
- Conditional Dependence
    
    - Given hidden causes `A`, `B` leading to event `C`, `A` and `B` are independent when there is NO information about `C` and become conditionally dependent given information about `C`
    - Again unintuitively, independence does NOT imply conditional independence!
- Bayes Networks Definition
    
    - Network of linked nodes defined by distributions of each individual node
        - Each node is only conditioned on linked incoming nodes
- Parameter Counts
    
    - REALLY big advantage of Bayes network: major reduction of values needed in calculation since can reduce from all possibilities from links! Compact, information-rich graph structure
        - Each node requires `2^k` variables, where `k` is number of inputs
- D-Separation
    
    - When determining dependence, track top-down/bottom-up linkings, with links breaking at known information. I.e. dependent if linked by only unknown information
    - D-separation = Reachability. Reachable->Dependent if in active triplet, not Reachable->Independent if in inactive triplet. Path is active if all active triplets, however only need 1 inactive triplet for inactive path. All inactive paths = independence (otherwise not guaranteed)!
        - ![4df34de6190774641c8c13dec5c7f2f1.png](:/882f5fbae92c4206bc45dc60c1140351)
- Probabalistic Inference from Bayes Net -- I.e. answering probabalistic questions using Bayes Net!
    
    - Can model as three types of nodes: Evidence, Hidden (internal calculation nodes), Query. Can arbitrarily assign these to form functions as long as we have enough given parameters to calculate!
    - Can generalize two types of questions: 1) what is probability of query given evidence? 2) given evidence, what is most likely query (take max over all possible queries)?
    - Enumeration: method of calculating all possible outcomes and adding them up
        - Make this more efficient by maximizing independence in Bayes net and arranging summation
    - Bayes Net is best for inference when written in Causal Direction (causes -> effects)
    - Variable Elimination techniques
        - 1.  Joining Factors (merge tables with similar variable dependence as joint probability tables, and thus a merged node!)
        - 2.  Summing-out (take merged node, and represent nodes using only one variable, so reduces a merged node to single node)
- Approximate Inference Sampling
    
    - To get approximate joint probabilities (instead of math computations), can sample your own distribution! (given the resource exists). Run large number of outcomes and record results, for large sample should converge to true value
        - Process is Consistent, since it converges to real result as samples approach infinity
    - Rejection sampling: similar process, though only keep samples that match criteria for evaluation. Also consistent, however can be computationally wasteful for unlikely events
        - Add Likelihood weighting: fix the evidence variable to desired value (i.e. the given), keep every sample (which leads to inconsistency), and then weight them with probabilities (which restores consistency). For probabilities, keep cumulative weight per sample using probability of the results we fix (i.e. it's not random, so account for that using the probability)
        - Likelihood weighting is good technique for simplifying cases where nodes have more parents
    - Gibbs Sampling
        - Uses Markov Chain Monte Carlo (MCMC) to sample. Idea: sample one variable randomly each iteration and fix all of the others. Repeat on iterations. Surprisingly converges to real distribution at large n!