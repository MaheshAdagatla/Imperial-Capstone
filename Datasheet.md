Datasheet for the Black-Box Optimisation (BBO) Capstone Project Dataset

##Motivation

This dataset supports a Black-Box Optimisation (BBO) task in which objective functions are unknown, expensive to evaluate, and accessible only through sequential queries. The primary aim is to study how optimisation strategies behave when the evaluation budget is severely constrained and decisions must be made under uncertainty.

The dataset enables analysis of exploration–exploitation trade-offs, surrogate modelling behaviour in low-data regimes, and the impact of adaptive decision-making across multiple rounds. These conditions closely mirror real-world optimisation problems such as hyperparameter tuning, scientific experimentation, simulation-based design, and industrial process optimisation.

##Composition

The dataset consists of input–output observations collected from eight independent black-box functions. Each function represents a separate optimisation task with its own dimensionality and response surface.

#Inputs

Each input is a continuous vector with dimensionality ranging from 2 to 8, depending on the function. All input values lie within the bounded domain [0,1]. Inputs are represented as floating-point values with up to six decimal places, reflecting the precision constraints imposed by the optimisation environment.

#Outputs

Each input corresponds to a single real-valued scalar output. All functions are treated as maximisation problems, although some outputs may be negative or transformed values. Higher values always indicate better performance relative to the optimisation objective.

##Dataset size and structure

The dataset is intentionally small. For each function, the total number of observations consists of an initial batch provided at the start of the challenge, followed by one additional observation per week over 13 optimisation rounds. As a result, each function contains only a few dozen data points at most.

This sparsity is a deliberate feature rather than a limitation, designed to reflect scenarios where evaluations are expensive and learning must occur with minimal data.

##Data organisation and location

All data is stored in the repository’s data folder: https://github.com/MaheshAdagatla/Imperial-Capstone/tree/main/data

The data folder contains:

Initial input and output files stored as NumPy .npy arrays for each function.

Incremental weekly inputs and outputs stored as text-based files, appended or versioned per optimisation round.

This separation allows the full optimisation trajectory to be reconstructed and analysed week by week, rather than only observing the final state.

##Gaps and limitations

Coverage of the input space is uneven by design. Early rounds include broader sampling, while later rounds concentrate observations near high-performing regions. Large portions of the domain may remain unexplored, particularly in higher dimensions.

No ground-truth global optima are provided. Performance must therefore be evaluated relative to observed improvements and convergence behaviour rather than absolute optimality.

##Collection Process

Data collection occurred through a strictly sequential optimisation process spanning 13 weeks.

Initial observations were provided as part of the challenge setup. Subsequent observations were generated one at a time using adaptive optimisation strategies informed by all prior data.

In early rounds, query generation emphasised exploration. Gaussian Process surrogates with uncertainty-aware acquisition functions encouraged sampling across the domain to learn global structure and reduce uncertainty.

In middle rounds, as patterns emerged, candidate selection became more structured. A neural network surrogate was introduced to complement the GP, supporting a two-stage selection process where uncertainty-aware screening was followed by nonlinear refinement.

In later rounds, query generation became increasingly localised around the best-known solutions. Sampling radii were reduced, exploration weights lowered, and weaker dimensions implicitly down-weighted based on feature relevance estimates.

Each optimisation round added at most one new observation per function, reflecting the strict evaluation budget.

##Preprocessing

Before training surrogate models, inputs are standardised using z-score normalisation to ensure numerical stability. Outputs are scaled using simple mean and variance normalisation where required, particularly for neural network training.

Normalisation parameters are retained to allow predictions to be interpreted in the original output scale. No data leakage occurs, as all preprocessing is performed using only data available up to the current round.

##Intended Uses

The dataset is intended for studying surrogate-based black-box optimisation under extreme data scarcity. It supports experimentation with Gaussian Processes, neural networks, acquisition functions, hybrid optimisation strategies, and adaptive exploration–exploitation control.

It is also suitable for educational purposes, demonstrating how optimisation strategies evolve over time and how decisions depend on uncertainty, budget awareness, and observed trends.

##Inappropriate Uses

The dataset is not suitable for supervised learning benchmarks that assume large, independent and identically distributed samples. It should not be used to make claims about general-purpose model performance or deployed decision-making systems without further validation.

The data is purely synthetic and abstract and is not intended for fairness, demographic, or societal impact analysis.

##Distribution and Maintenance

The dataset is publicly available as part of the project’s GitHub repository and is distributed alongside the associated notebooks and documentation to ensure full reproducibility.

Maintenance is performed by the project author. No new data will be added beyond the completed 13 optimisation rounds. Any corrections or clarifications to data formatting or preprocessing are documented directly in the repository to preserve transparency and traceability.
