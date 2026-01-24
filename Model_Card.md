Model Card: Hybrid Two-Stage Surrogate-Based Black-Box Optimisation
1. Overview

Model name: Hybrid GP–NN Two-Stage BBO Optimiser
Type: Sequential surrogate-based black-box optimisation
Version: v0.07 (11-round iterative refinement)

This approach combines a Gaussian Process (GP) surrogate and a Neural Network (NN) surrogate in a two-stage candidate selection pipeline. The GP is used for uncertainty-aware screening, while the NN refines promising candidates based on learned nonlinear structure. The approach was not fixed upfront; it evolved week by week in response to observed optimisation behaviour under strict query limits.

2. Intended Use

This approach is suitable for:

Black-box optimisation with very limited evaluation budgets

Continuous, bounded search spaces

Expensive or slow-to-evaluate objective functions

Educational and research settings exploring exploration–exploitation trade-offs

It is not suitable for:

Discrete or categorical optimisation problems

Large-scale supervised learning settings

Highly discontinuous or adversarial objective functions

Scenarios requiring guarantees of global optimality

3. Approach Details and Evolution Across Rounds

The optimisation strategy evolved deliberately over the rounds.

Early rounds (exploration-first):
I prioritised broad coverage of the search space using uniform sampling across all dimensions. A GP with a Matérn kernel served as the primary surrogate, and acquisition behaviour favoured exploration to reduce uncertainty and avoid early commitment to misleading regions.

Middle rounds (balanced refinement):
As patterns emerged, I introduced a Neural Network surrogate to complement the GP. A two-stage pipeline was adopted: the GP shortlisted candidates using uncertainty-aware scoring, and the NN re-ranked them based on learned nonlinear trends. At this stage, decisions relied more on surrogate agreement than raw uncertainty alone.

Later rounds (controlled exploitation):
Once around 60–70% of the query budget was consumed, I shifted toward controlled exploitation. Candidate generation became increasingly localised around the current best solutions, using weighted sampling and shrinking radii while still preserving some diversity. Dimension importance estimates from GP lengthscales and NN sensitivities guided how aggressively each dimension was narrowed.

By round 11, the strategy is intentionally conservative: refining known good regions rather than chasing uncertain global improvements.

4. Performance

Evaluation context:

Eight independent black-box functions

Strictly limited number of queries per function

No access to true optima or gradients

Metrics used:

Best observed function value per round

Improvement relative to early baselines

Stability of surrogate predictions during exploitation

Qualitative convergence patterns rather than formal regret bounds

Summary:

Most functions showed steady improvement in early and mid rounds

Diminishing returns became apparent after mid-budget usage

In some cases, predicted improvements during exploitation were slightly below the current best, reflecting uncertainty and surrogate bias rather than incorrect logic

5. Assumptions and Limitations

Key assumptions:

The objective functions are reasonably smooth and continuous

Local optima provide useful signals for further refinement

Surrogate uncertainty remains informative despite very small datasets

Limitations:

High sensitivity to early samples in the low-data regime

Risk of sampling bias toward regions favoured by surrogate assumptions

No guarantees of convergence or optimality

Performance depends strongly on correct scaling and normalisation

Potential failure modes include premature convergence, overconfidence in local regions, and missed optima in sparsely explored areas.

6. Ethical and Transparency Considerations

I prioritised transparency by documenting:

Query choices and their rationale

Model assumptions

Hyperparameter changes

Weekly strategy adjustments

This supports reproducibility and allows others to follow, critique, or adapt the approach. Although the task is synthetic, the discipline of transparent optimisation mirrors real-world ML practice, where opaque decision-making can lead to unreliable or biased outcomes.

7. Reflections on Clarity and Usefulness

This model card is designed to reflect how decisions were actually made, rather than presenting a polished, static algorithm. The focus on evolution, constraints, and reasoning makes the approach easier to understand and reproduce. Adding further low-level technical detail would not materially improve clarity given the small-data setting and exploratory nature of the project.
