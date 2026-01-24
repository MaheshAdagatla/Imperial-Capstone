# Imperial-Capstone
Repository for the Black-Box Optimisation Capstone Project

##Project Overview

This repository contains the work developed over thirteen weeks for the Black-Box Optimisation (BBO) Capstone Project. The project investigates how to efficiently optimise unknown and potentially complex objective functions when no analytical form, gradients, or internal structure are available. Each objective behaves as a true black box: the optimiser can only query the function at selected input values and observe a scalar output in response.

This setting closely mirrors many real-world optimisation problems such as hyperparameter tuning, materials discovery, drug design, and industrial process optimisation, where evaluations are expensive, noisy, and strictly limited. The central challenge is therefore not raw optimisation power, but data efficiency: identifying high-performing inputs while making as few function calls as possible.

Rather than treating optimisation as a one-shot algorithmic choice, the project was approached as an iterative reasoning exercise. The optimisation strategy was refined week by week in response to observed behaviour of the functions, surrogate model diagnostics, and diminishing returns as the query budget was consumed. This reflects realistic ML and research workflows, where decisions evolve as evidence accumulates and early assumptions are revised.

From a professional perspective, the project builds practical capability in uncertainty-aware modelling, experimental design, surrogate-based reasoning, and adaptive decision-making under tight constraints.

Problem Setting and Objectives

The task consists of eight independent black-box optimisation problems. Each function accepts a continuous-valued input vector and returns a single real-valued score. The dimensionality of the input varies by function, ranging from two to eight dimensions.

All problems are strict maximisation tasks. For each function, the total evaluation budget is limited to thirteen queries, including the initial provided observations. Queries are strictly sequential: each new input must be chosen using only the information available from all previous evaluations.

Additional constraints apply to all inputs. Each input vector must begin with a zero-valued element, all values must be continuous, and numerical precision is limited to a maximum of six decimal places. These constraints enforce careful input construction and prevent brute-force or randomised search strategies.

The core objective across all weeks is to maximise the final achieved score for each function while demonstrating principled reasoning, stability, and adaptability in the optimisation process.

Repository Structure

The repository is organised to clearly separate data, experimentation, and documentation.

The Data folder contains all inputs and outputs for the project, including the initial provided observations and the results of each weekly query for all functions. This includes both intermediate and final datasets, allowing the full optimisation trajectory to be reconstructed and analysed.

The Notebooks folder contains all Jupyter notebooks used throughout the project. These notebooks include exploratory analysis, surrogate model development, acquisition strategy testing, and diagnostic visualisations. Wherever notebooks allow configuration of the target function number, this parameter should be updated to the specific function being optimised before execution.

The remaining files provide structured documentation of the project. The README presents the high-level narrative and evolution. The Methodology document explains the reasoning behind design choices and how they changed over time. The Model Card documents the final hybrid optimisation approach and its assumptions. The Datasheet records the structure, constraints, and evolution of the input–output data.

Inputs and Outputs

Each black-box function accepts a continuous numeric input vector of the form [x1, x2, …, xn], where n depends on the function. For example, Function 1 operates in two dimensions, while Function 8 operates in eight dimensions. In all cases, the first input value is fixed to zero, and remaining values must respect the specified numerical precision constraints.

Each query produces a single scalar output. Higher values indicate better performance, and no additional metadata or uncertainty estimates are provided by the black-box functions themselves. All uncertainty handling is therefore internal to the optimiser.

A complete record of all inputs and outputs, including initial data and weekly additions, is maintained in the Data folder and documented in detail in the Datasheet file.

Optimisation Journey from Week 1 to Week 13

The optimisation strategy evolved continuously over the thirteen weeks, moving from broad exploration to focused exploitation as more information became available and the remaining budget shrank.

In the early weeks, the primary goal was to understand the global structure of each function. With very limited initial data, the risk of premature convergence was high. During this phase, the optimiser prioritised exploration across the full input domain. Gaussian Process surrogate models with Matérn kernels were used to capture smooth but flexible response surfaces while maintaining calibrated uncertainty estimates. Acquisition behaviour was tuned to favour uncertainty reduction and information gain rather than immediate performance.

As the project progressed into the middle weeks, sufficient data had accumulated to support more nuanced decision-making. The strategy shifted toward a balanced exploration–exploitation regime. A neural network surrogate was introduced alongside the Gaussian Process to capture more complex or non-stationary patterns that the GP might smooth over. A two-stage candidate selection process was adopted, with the GP used to screen broadly promising regions and the neural network used to rank candidates based on predicted performance. Decisions were increasingly guided by agreement between surrogates rather than uncertainty alone.

In the later weeks, the remaining query budget became the dominant constraint. At this stage, the strategy focused on controlled exploitation and local refinement. Sampling became progressively localised around the best-performing regions identified so far. Dimension relevance was estimated using GP length-scales and neural network sensitivity analysis, allowing weaker or less influential dimensions to be narrowed more aggressively while preserving the full input structure. Small, deliberate exploration steps were retained to guard against overconfidence and surrogate bias, but the emphasis was firmly on extracting maximum value from each remaining evaluation.

By week thirteen, the optimisation process had converged into a tightly focused local search guided by accumulated evidence, surrogate agreement, and stability considerations rather than aggressive exploration.

Workflow and Execution

Each optimisation iteration follows a consistent workflow. Existing input–output data are loaded and normalised from the Data folder. Surrogate models are trained or updated using all available observations. Candidate points are generated and scored using the current acquisition strategy. A single candidate is selected and evaluated against the black-box function. The resulting input and output are logged and added to the dataset. Diagnostics are then reviewed to assess convergence, uncertainty behaviour, and feature sensitivity before proceeding to the next iteration.

This structure ensures reproducibility while allowing strategic adjustments between weeks as new evidence emerges.

Surrogate Models and Strategy

The optimisation framework relies on a combination of surrogate models rather than a single fixed approach. Gaussian Processes play a central role throughout the project due to their ability to operate effectively in low-data regimes and provide uncertainty estimates that directly support exploration decisions. Matérn kernels are used to balance smoothness and flexibility, and hyperparameters are re-optimised as new data arrive.

Neural network surrogates are introduced later in the project to complement the GP. These models are intentionally kept small to avoid overfitting and are used primarily for ranking and sensitivity analysis rather than uncertainty estimation. Together, the two models enable a hybrid strategy that combines principled uncertainty handling with expressive nonlinear modelling.

Hyperparameter Adaptation

Hyperparameters were not fixed at the outset of the project. Instead, they were adapted gradually as the optimisation progressed. Gaussian Process kernel parameters, noise levels, and length-scales were re-estimated regularly to reflect the growing dataset. Acquisition parameters controlling exploration pressure were reduced over time to shift the balance toward exploitation. Neural network capacity and training settings were adjusted to prioritise stability in the low-data regime.

Two-stage optimisation parameters, such as candidate pool sizes and local sampling radii, were also refined to reflect increasing confidence in the location of high-performing regions. These adjustments were driven by observed behaviour, surrogate diagnostics, and diminishing marginal gains rather than rigid heuristics.

How to Use This Repository

Readers interested in the full optimisation narrative should begin with this README, then refer to the Methodology document for detailed reasoning behind design decisions. The Model Card provides a structured description of the final hybrid optimiser and its limitations. The Datasheet documents the structure and evolution of the data used throughout the project.

For hands-on exploration, the Data folder contains all recorded inputs and outputs, and the Notebooks folder contains executable analyses and experiments. When running notebooks, ensure that any configurable function number is updated to match the specific black-box function of interest.

Together, these materials aim to document not only the final results, but the full reasoning process behind them, reflecting realistic optimisation under uncertainty rather than a static or idealised solution.

