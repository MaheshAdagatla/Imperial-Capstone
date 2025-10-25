# Imperial-Capstone
Repository for the capstone project

Section 1: Project Overview
The Black-Box Optimisation (BBO) Capstone Project explores how to efficiently optimise unknown, complex functions when direct analytical forms are unavailable. Each function behaves like a “black box” — you can query it at specific input values and receive a response, but you don’t know the underlying relationship. This mirrors many real-world machine learning and industrial problems, such as hyperparameter tuning, materials discovery, drug design, or process optimisation, where evaluating the objective is expensive and limited.
The overall goal is to find the input that maximises the unknown function’s output while keeping the number of queries (evaluations) as low as possible. Because every function call is costly, the project focuses on learning efficiently from limited data — making intelligent decisions about where to sample next.
In career terms, this project builds practical strengths in sequential decision-making, uncertainty quantification, and experimental design. It sharpens the ability to operate with incomplete knowledge — a vital skill for data scientists, ML engineers, and research professionals who must design, test, and iterate with limited data or budget.

Section 2: Inputs and Outputs
Each of the eight black-box functions in the challenge receives a numeric input vector and returns a scalar performance value.

Inputs (queries):
Structured as continuous feature vectors [x1, x2 ... xn]
Each function has a different dimensionality (d ranges from 2 to 8).

Example:
Function 1 input: [0.42, 0.78]
Function 8 input: [0.35, 0.60, 0.12, 0.95, 0.44, 0.38, 0.72, 0.15]

Constraints:
Inputs must start with 0
Inputs must have a maximum 6 decimals
Queries are sequential — one function call per iteration.

Outputs (responses):
Each query returns a single real-valued score representing the function’s performance at that input.
All tasks are maximisation problems — higher values indicate better performance.
Example: Input [0.42, 0.78] → Output 0.67.

The model’s goal is to choose the next input intelligently to improve this output, given limited prior observations (often only 10–40 data points to start).

Section 3: Challenge Objectives
The objective is to maximise the unknown function using the limited number of evaluations available.

Key constraints include:
Limited query budget: Only 13 total evaluations per function (including the initial data).
Sequential feedback: Each new input must be chosen based on results so far.
Unknown function structure: The mapping from inputs to outputs is nonlinear, possibly multimodal, and noisy.
This setup tests the ability to balance exploration (searching new areas to learn about the function) and exploitation (refining around known high-performing regions). The challenge lies in achieving the global maximum efficiently while managing uncertainty and cost.

Section 4: Technical Approach
During the first three query submissions, the approach has evolved from exploration-driven to model-guided optimisation:

Modelling technique:
A Gaussian Process (GP) surrogate model with a Matern kernel models the unknown function.
The GP provides both mean predictions and uncertainty estimates, enabling informed decisions about where to query next.

Acquisition strategy:
The Expected Improvement (EI) function guides the search.
Early iterations emphasised exploration (higher exploration parameter values, e.g., 0.01–0.05) to discover promising regions.
Later iterations will shift toward exploitation (smaller exploration parameter values, eg. 0.001 or using Probability of Improvement) to fine-tune around the best region.

Hyperparameter handling:
GP hyperparameters (length-scales, variance, noise) are re-optimised after each iteration.
High length-scale bounds help detect and down-weight irrelevant dimensions.

Feature sensitivity:
Sensitivity analysis assesses feature influence; features with minimal impact may be deprioritised to improve efficiency.

Alternative considerations:
Explored SVM classification and feature relevance analysis to identify high vs low performance regions, but these serve mainly as diagnostic aids rather than replacements for the GP.
Exploration vs exploitation balance:

Current strategy still includes limited exploration in uncertain regions but increasingly trusts the model’s predictions as more data accumulates.
The switch from broad exploration to local refinement is planned dynamically based on Expected Improvement convergence.
