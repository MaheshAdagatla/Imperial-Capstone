Model Card: Sequential Surrogate-Based Black-Box Optimisation Framework

##Overview

Model name: Sequential GP and Hybrid GP–NN Black-Box Optimiser
Model type: Sequential surrogate-based black-box optimisation
Project duration: 13 rounds of iterative refinement
Evaluation setting: Strictly budget-limited, sequential queries

This model card documents the full optimisation framework used in the Black-Box Optimisation (BBO) Capstone Project. The approach did not consist of a single static model. Instead, it evolved through two distinct but connected phases: an initial Gaussian Process (GP)–only surrogate model, followed by a hybrid two-stage GP and Neural Network (NN) surrogate framework.

The evolution reflects increasing data availability, reduced uncertainty, and the need to transition from exploration to controlled exploitation under a fixed and shrinking query budget.

##Intended Use

The optimisation framework is intended for problems with unknown objective functions that are expensive or slow to evaluate, where only a small number of sequential queries are permitted. Typical use cases include hyperparameter tuning, scientific experimentation, simulation-based optimisation, and educational settings exploring exploration–exploitation trade-offs.

The approach is well suited to continuous, bounded input spaces with dimensionality ranging from low to moderately high, where uncertainty-aware decision-making is critical.

It is not designed for discrete or categorical optimisation problems, large-scale supervised learning tasks, or scenarios requiring formal guarantees of global optimality.

##Phase 1: Gaussian Process–Only Surrogate Model

#Description

In the early rounds of the project, optimisation relied exclusively on a Gaussian Process surrogate model. The GP was selected due to its strong performance in low-data regimes and its ability to produce calibrated uncertainty estimates alongside predictions.

A Matérn kernel was used to allow flexibility in modelling both smooth and moderately irregular objective functions. At this stage, the GP served primarily as an exploratory model rather than a precision optimiser.

Role in the optimisation process

The GP-only model guided early query selection by balancing predicted performance with predictive uncertainty. Acquisition behaviour prioritised exploration to improve global understanding of the function and reduce the risk of premature convergence.

This phase focused on identifying broad trends, detecting promising regions, and observing which input dimensions appeared influential or weak. Short-term performance gains were deliberately deprioritised in favour of information gathering.

#Strengths and limitations

The GP-only approach provided strong uncertainty awareness and robustness with very limited data. However, as more observations accumulated, the GP began to smooth over local nonlinearities and showed diminishing marginal returns in candidate ranking. These limitations motivated the introduction of a complementary surrogate model.

##Phase 2: Hybrid GP and Neural Network Two-Stage Model

#Description

From the mid rounds onward, a neural network surrogate was introduced alongside the GP, forming a hybrid two-stage optimisation framework. The GP retained responsibility for uncertainty-aware screening, while the NN was used to refine and rank promising candidates based on learned nonlinear structure.

The NN was implemented with deliberately limited capacity to avoid overfitting in the small-data regime. It was never used for global search or uncertainty estimation.

#Two-stage candidate selection

In the hybrid framework, candidate selection proceeds in two steps. First, a broad pool of candidate points is evaluated using the GP acquisition function, with exploration weights gradually reduced over time. Second, a smaller subset of high-potential candidates is re-ranked using the NN’s predicted mean.

This separation allows uncertainty to guide where the optimiser looks, while expressiveness determines how fine-grained decisions are made within promising regions.

#Feature relevance and dimensional control

Both surrogates contribute to feature relevance estimation. GP kernel length scales are interpreted as indicators of global relevance, while NN input sensitivities provide local importance estimates.

Rather than removing dimensions, weaker features are down-weighted during candidate generation and sampling radius control. This preserves the full input structure while improving efficiency and stability.

#Final rounds behaviour

In later rounds, the hybrid model operates in a controlled exploitation regime. Candidate sampling is localised around the current best solution, with shrinking radii and reduced exploration coefficients. The GP ensures conservative decision-making, while the NN performs fine-grained ranking within a trusted region.

##Performance Evaluation

#Evaluation context

The optimiser was evaluated across eight independent black-box functions with dimensionality ranging from two to eight. Each function had a strictly limited number of allowed queries, and no access to gradients or true optima was available.

#Performance indicators

Performance was assessed using best observed function values over time, improvement relative to early baselines, stability of surrogate predictions in later rounds, and qualitative convergence behaviour rather than formal regret bounds.

#Observed outcomes

Across most functions, the GP-only phase produced reliable early improvements and structural insight. The hybrid phase improved refinement efficiency and stabilised late-stage decisions. Diminishing returns were observed as the query budget was exhausted, which is consistent with expectations in severe low-budget optimisation settings.

##Assumptions and Limitations

The framework assumes that objective functions exhibit some degree of continuity and local smoothness, and that early observations provide meaningful signals for later refinement. It also assumes that feature relevance inferred from limited data is sufficiently informative to guide sampling decisions.

Limitations include sensitivity to early samples, potential bias introduced by surrogate assumptions, and the absence of guarantees regarding convergence or global optimality. The approach may underperform on highly deceptive landscapes or functions with isolated optima far from early samples.

##Ethical and Transparency Considerations

All optimisation decisions, hyperparameter adjustments, and strategy transitions were documented throughout the project. This transparency supports reproducibility, critical review, and adaptation by others.

Although the task itself is synthetic, the emphasis on traceability, uncertainty awareness, and explicit reasoning mirrors best practices in real-world ML and optimisation work, where opaque decision-making can lead to unreliable outcomes.

##Reflections on Usefulness

This model card intentionally reflects the evolution of the optimisation strategy rather than presenting a polished, static algorithm. The distinction between the initial GP-only phase and the later hybrid GP–NN phase is central to understanding how and why decisions changed as data accumulated and constraints tightened.

The framework prioritises adaptability, reasoning, and robustness over raw optimisation performance, aligning with the realities of constrained optimisation in professional and research settings.
