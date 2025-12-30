Methodology.md
1. Purpose of This Document

This document records the methodological reasoning behind the Black-Box Optimisation (BBO) Capstone Project and how that reasoning evolved over successive rounds. Rather than describing a single fixed algorithm, it documents an adaptive optimisation strategy that changed as more evidence was gathered and the remaining query budget decreased. The aim is to allow another researcher to understand not only what was done, but why the approach changed over time and how those changes were justified.

2. Problem Understanding

The task is to optimise unknown, expensive-to-evaluate functions under a strict query budget. Each function can only be queried a limited number of times, and no assumptions are made about smoothness, modality, or noise beyond what can be inferred from observations. Because early decisions strongly shape later outcomes, the optimisation strategy must balance information gathering with performance improvement and adapt as uncertainty reduces.

3. Initial Strategy (Early Rounds: Broad Exploration)
Modelling Choice

In the early rounds, Gaussian Processes (GPs) were selected as the primary surrogate model due to their strong performance in low-data settings and their ability to quantify predictive uncertainty. A Matérn kernel was used to remain flexible to both smooth and moderately rough response surfaces.

At this stage, the goal was coverage rather than precision. The GP was treated primarily as a tool for understanding the global structure of the function rather than for precise optimisation.

Acquisition Behaviour

Early acquisition emphasised exploration. Uncertainty-aware acquisition functions were prioritised so that regions with high predictive variance would be sampled, even if their predicted mean was modest. This reduced the risk of prematurely committing to a suboptimal region and helped identify whether the function exhibited strong boundary effects, symmetry, or flat regions.

4. Intermediate Strategy (Mid Rounds: Structured Exploration)
Motivation for Change

By the mid rounds, repeated evaluations began to reveal consistent patterns:

Certain regions were repeatedly high-performing.

Some input dimensions appeared weakly influential.

Purely global exploration produced diminishing returns.

These observations motivated a shift toward structured exploration.

Dual-Surrogate Introduction

A neural network (NN) surrogate was introduced alongside the GP. The GP retained responsibility for uncertainty estimation and global guidance, while the NN provided a more flexible approximation of complex or non-stationary behaviour. At this stage, the NN was not trusted for global search, but it proved useful for comparing candidates already deemed promising.

Feature Sensitivity Analysis

Feature importance estimates were extracted from both models:

GP length scales were interpreted as relevance indicators.

NN input gradients were used to assess local sensitivity.

Rather than removing dimensions, less influential features were implicitly down-weighted during candidate generation. This preserved the full input structure while improving sampling efficiency.

5. Transition Strategy (Later Rounds: Exploration → Exploitation)
Budget Awareness

As approximately 60–70% of the query budget was consumed, the optimisation objective shifted. The emphasis moved from learning the global structure of the function to refining the best regions already identified. At this point, continued broad exploration was unlikely to outperform focused refinement.

Two-Stage Candidate Selection

A two-stage selection process was adopted:

A large candidate set was evaluated using the GP with a reduced exploration weight.

A smaller shortlist was re-ranked using the NN’s predicted mean.

This approach retained uncertainty awareness while increasingly favouring high-mean regions.

6. Current Strategy (Final Rounds: Controlled Exploitation)
Localised Candidate Sampling

In the final rounds, candidate generation was restricted to a local neighbourhood around the current best-known solution. The sampling radius was progressively reduced to control exploitation and avoid overly greedy steps. Feature importance continued to shape how tightly each dimension was sampled.

Acquisition Tuning

The GP acquisition function was made deliberately more exploitative by lowering the uncertainty coefficient. This reflects increased confidence in the surrogate models and the high cost of exploratory queries late in the budget.

Role of the Neural Network

The NN now plays a decisive role in fine-grained selection. Rather than guiding exploration, it acts as a local ranking mechanism within a carefully chosen region. This mirrors practical optimisation settings where coarse global models hand off to local refiners near convergence.

7. Hyperparameter Adaptation Across Rounds

Hyperparameters were adjusted dynamically rather than fixed:

Candidate pool sizes decreased over time.

Exploration weights were reduced steadily.

Local sampling radii were introduced and tightened.

NN batch sizes were reduced to emphasise sharper local decisions.

These changes were guided by observed convergence behaviour rather than predefined schedules.

8. Reflections and Limitations

This evolving methodology assumes that local smoothness exists near good solutions and that feature relevance inferred from limited data is meaningful. While the adaptive strategy reduces wasted queries, it may still miss isolated global optima or underperform on highly deceptive landscapes. These risks were mitigated through controlled, rather than greedy, exploitation in the final rounds.
