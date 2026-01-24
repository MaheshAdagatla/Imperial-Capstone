Datasheet for the Black-Box Optimisation (BBO) Capstone Project Dataset
1. Motivation

This dataset was created to support a Black-Box Optimisation (BBO) task where the objective function is unknown, expensive to evaluate, and only accessible through sequential queries. The primary goal is to study how different optimisation strategies (Gaussian Processes, Neural Networks, acquisition functions, and hybrid approaches) perform under strict query budgets. The dataset supports experimentation in sequential decision-making, uncertainty-aware optimisation, and exploration–exploitation trade-offs, which mirror real-world problems such as hyperparameter tuning, scientific experimentation, and design optimisation.

2. Composition

The dataset consists of input–output pairs collected from eight independent black-box functions.

Inputs (X):

Continuous vectors with dimensionality ranging from 2 to 8.

Each input lies in the bounded domain [0,1] for d dimensions

Values are represented as floating-point numbers with up to six decimal places.

Outputs (y):

A single real-valued scalar per input.

All functions are maximisation tasks.

Size:

Each function has a small number of observations, typically between 10 and 18 data points after multiple weeks.

The dataset is intentionally sparse to reflect expensive evaluation settings.

Gaps and limitations:

Coverage of the input space is uneven due to adaptive querying.

Some regions are densely sampled near optima, while others remain unexplored.

No ground-truth global optimum labels are available.

Format:

Stored as NumPy .npy files for initial data.

Incremental data is available as .txt files for each week.  The data is appended programmatically within the optimisation loop.

3. Collection Process

The data was collected through a sequential querying process over multiple weeks.

Query generation:

Initial points were provided as part of the challenge.

Subsequent queries were generated adaptively using surrogate models.

Early rounds prioritised exploration using wide Monte Carlo sampling and higher acquisition uncertainty.

Later rounds shifted toward exploitation using two-stage selection (GP-based shortlist followed by NN re-ranking).

Strategies used:

Gaussian Process surrogate with a Matérn kernel for uncertainty estimation.

Neural Network surrogate for flexible function approximation.

Acquisition-driven sampling (UCB, Expected Improvement concepts).

Dimension importance weighting and localised sampling in later stages.

Time frame:

Data was collected incrementally across approximately ten weekly optimisation rounds.

Each round added at most one new query per function due to budget constraints.

4. Preprocessing and Uses

Preprocessing:

Input features are standardised using z-score normalisation before training surrogate models.

Outputs are manually normalised (mean subtraction and variance scaling) to stabilise training.

Normalisation parameters are stored to allow inverse transformation of predictions.

Intended uses:

Training and evaluating surrogate models under low-data conditions.

Studying exploration–exploitation dynamics in black-box optimisation.

Comparing GP-based, NN-based, and hybrid optimisation strategies.

Educational use in demonstrating practical Bayesian optimisation concepts.

Inappropriate uses:

Supervised learning benchmarks requiring large, i.i.d. datasets.

Claims about general-purpose model performance or real-world deployment without further validation.

Fairness, demographic, or societal impact analysis (the data is purely synthetic and abstract).

5. Distribution and Maintenance

Availability:

The dataset is available within the project’s GitHub repository as part of the capstone submission.

Data is distributed alongside code to ensure reproducibility of results.

Terms of use:

Intended for academic, educational, and non-commercial research use.

Users are expected to cite the project context if reused in reports or coursework.

Maintenance:

The dataset is maintained by the project author.

Updates occur only when new optimisation rounds are completed.

Any changes to preprocessing or data collection logic are documented in the repository to preserve transparency.
