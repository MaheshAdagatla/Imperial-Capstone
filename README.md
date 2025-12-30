# Imperial-Capstone
Repository for the Black-Box Optimisation Capstone Project
Section 1: Project Overview

The Black-Box Optimisation (BBO) Capstone Project investigates how to efficiently optimise unknown, complex functions when no analytical form or gradients are available. Each objective behaves like a black box: it can be queried at chosen input values and returns a scalar response, but the underlying relationship is hidden.

This setting mirrors real-world problems such as hyperparameter tuning, materials discovery, drug design, and industrial process optimisation, where evaluations are expensive and strictly limited. The core challenge is to identify high-performing inputs while making as few function calls as possible.

The project emphasises sequential, data-efficient decision-making under uncertainty. Rather than fixing a single algorithm upfront, the optimisation strategy was iteratively refined across multiple rounds in response to observed behaviour. This reflects realistic ML and research workflows, where strategies adapt as evidence accumulates.

From a professional perspective, the project builds practical capability in uncertainty-aware modelling, experimental design, and iterative reasoning — key skills for data scientists, ML engineers, and researchers working under tight data or budget constraints.

Section 2: Inputs and Outputs

(Refer to /Datasheet.txt for full data documentation.)

Each of the eight black-box functions accepts a numeric input vector and returns a single scalar output.

Inputs (queries):

Continuous feature vectors: [x1,x2,…,xn]

Dimensionality varies by function (d ranges from 2 to 8)

Examples:

Function 1 input: [0.42, 0.78]

Function 8 input: [0.35, 0.60, 0.12, 0.95, 0.44, 0.38, 0.72, 0.15]

Constraints:

Inputs must start with 0

Inputs must have a maximum of 6 decimal places

Queries are strictly sequential (one evaluation per iteration)

Outputs (responses):

A single real-valued score per query

All tasks are maximisation problems (higher is better)

The optimiser’s role is to select the next input intelligently, given very limited prior observations (often fewer than 40 points).

Section 3: Challenge Objectives

The objective is to maximise each unknown function under a strict evaluation budget.

Key constraints:

Limited query budget: 13 total evaluations per function (including initial data)

Sequential feedback: Each query must depend on all previous results

Unknown structure: Functions may be nonlinear, multimodal, and noisy

This setup explicitly tests the ability to balance exploration (learning about unobserved regions) and exploitation (refining known high-performing regions). Success depends on extracting maximal information from each evaluation rather than brute-force search.

Section 4: Technical Approach and Its Evolution

Rather than using a fixed optimisation strategy, the approach evolved across rounds as more information became available.

Early rounds (exploration-first):

Broad sampling across the full input domain

Gaussian Process (GP) surrogate with a Matérn kernel

Acquisition behaviour prioritised uncertainty reduction to avoid premature commitment

Middle rounds (balanced refinement):

Introduction of a Neural Network (NN) surrogate to capture nonlinear patterns

Two-stage candidate selection: GP-based screening followed by NN re-ranking

Decisions increasingly guided by agreement between surrogates rather than uncertainty alone

Later rounds (controlled exploitation):

Sampling progressively localised around the best-performing regions

Dimension importance estimated via GP length-scales and NN sensitivities

Weaker dimensions narrowed more aggressively while preserving full input structure

Small, deliberate exploration retained to avoid overconfidence

This progression reflects a shift from global learning to local refinement as the query budget is consumed.

Section 5: Workflow Overview

Each optimisation iteration follows a consistent loop:

Data Preparation – Load and normalise existing input–output pairs

Model Fitting – Train the current surrogate model(s)

Acquisition Step – Score candidate points using an acquisition function

Evaluation and Logging – Query the black-box function and record results

Analysis and Monitoring – Inspect convergence, uncertainty, and feature sensitivity

This structure ensures reproducibility while allowing strategy adjustments between rounds.

Section 6: Surrogate Modelling Approaches

(Refer to /Model_Card.txt for detailed model documentation.)

The repository includes multiple surrogate models:

Gaussian Process (GP):
Uses a Matérn kernel to model smooth, nonlinear surfaces with calibrated uncertainty. Well suited to low-data regimes and central to early-stage decision-making.

Neural Network Surrogate:
Implemented in PyTorch to capture complex or non-stationary patterns. Gradient-based sensitivity analysis is used to assess feature influence and guide sampling during later rounds.

The combination allows uncertainty-aware exploration early on and expressive modelling during exploitation.

Section 7: Hyperparameter Tuning

Several hyperparameters critically influence model stability, convergence speed, and the exploration–exploitation balance. These parameters were not fixed upfront; instead, they were adjusted gradually as the optimisation progressed and the query budget diminished.

Gaussian Process (GP) hyperparameters
Examples include:

Kernel length scales (per dimension), used to control smoothness and detect irrelevant inputs

Kernel variance, governing the overall output scale

Observation noise level, allowing robustness to noisy evaluations
These parameters are re-optimised via marginal likelihood after each iteration to ensure the GP adapts as new data arrive.

Acquisition function hyperparameters
Examples include:

Expected Improvement (EI) / Probability of Improvement (PI): exploration parameter xi, reduced over rounds to shift from exploration to exploitation

Upper Confidence Bound (UCB): confidence parameter k, tuned downward to limit over-exploration as convergence approaches
These parameters directly control how aggressively the optimiser explores uncertain regions versus refining known high-performing areas.

Neural Network surrogate hyperparameters
Examples include:

Number of hidden layers and neurons per layer

Learning rate and optimiser choice

Batch size (reduced in later rounds to stabilise exploitation)
Neural network capacity was intentionally kept minimal to avoid overfitting in the low-data regime.

Two-stage optimisation and exploitation parameters
Examples include:

Stage 1 candidate pool size (broad GP-based screening)

Stage 2 refinement size (focused NN-based ranking)

Local sampling radius around the current best solution

Retention of the current best point to prevent regression
These parameters increasingly favoured local refinement in later rounds while maintaining a small degree of diversity.

Overall, hyperparameter tuning prioritised robustness, interpretability, and controlled convergence over aggressive optimisation. Adjustments were guided by observed surrogate behaviour, acquisition stability, and diminishing marginal gains rather than fixed heuristics.

Section 8: How to Read This Repository

README.md – High-level overview and project narrative

Methodology.md – Rationale and evolution of design decisions

Model_Card.txt – Detailed description of the final hybrid optimiser

Datasheet.txt – Documentation of inputs, outputs, and constraints

Together, these documents aim to present not just what was built, but how and why it evolved — reflecting realistic optimisation under uncertainty rather than a static, idealised solution.
