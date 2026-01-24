##Purpose of This Document

This document describes the methodological reasoning behind the Black-Box Optimisation (BBO) Capstone Project and how that reasoning evolved over thirteen weeks of sequential optimisation. Rather than presenting a single fixed algorithm, it documents an adaptive strategy that changed in response to observed data, surrogate behaviour, and a progressively shrinking query budget.

The intention is to make the optimisation process transparent and reproducible. A reader should be able to understand not only what decisions were made at each stage, but why those decisions were justified at the time, given the available evidence and constraints.

##Problem Framing and Constraints

The core task is to optimise unknown, expensive-to-evaluate functions under a strict and limited evaluation budget. Each function can only be queried sequentially, and no assumptions are made about its analytical form, smoothness, modality, or noise beyond what can be inferred from observed data.

Early decisions strongly influence later outcomes because each query permanently consumes part of the budget. This makes the optimisation problem inherently path-dependent. As a result, the strategy must balance learning about the function’s structure with improving performance, and must adapt continuously as uncertainty reduces and remaining opportunities narrow.

Because the number of available evaluations is extremely small relative to the dimensionality of some functions, the methodology prioritises data efficiency, uncertainty awareness, and robustness over aggressive optimisation.

##Initial Strategy: Early Rounds and Broad Exploration

In the early rounds, the primary objective was to gain a coarse but reliable understanding of each function’s global behaviour. At this stage, uncertainty was high and the cost of premature exploitation was severe.

Gaussian Processes were selected as the initial surrogate model due to their strong performance in low-data regimes and their ability to provide calibrated predictive uncertainty. A Matérn kernel was used to allow flexibility across a range of smoothness assumptions without overcommitting to a highly smooth response surface.

During this phase, the surrogate model was treated primarily as an exploratory tool rather than an optimiser. Acquisition behaviour emphasised uncertainty-aware sampling, prioritising regions with high predictive variance even when predicted performance was modest. This helped reveal whether promising regions were isolated or persistent, whether boundary effects were present, and whether the function exhibited large flat or weakly informative regions.

The emphasis was on coverage rather than precision. Accepting short-term suboptimal evaluations was viewed as a necessary cost to reduce structural uncertainty and avoid early lock-in to misleading local optima.

##Intermediate Strategy: Structured Exploration and Model Enrichment

As the dataset grew, repeated evaluations began to reveal stable patterns. Certain regions consistently outperformed others, while some input dimensions appeared to have limited influence on the output. At the same time, purely global exploration produced diminishing informational returns.

These observations motivated a shift toward more structured exploration. The goal was no longer to understand the entire domain equally, but to focus learning effort where it was most likely to improve final performance.

At this stage, a neural network surrogate was introduced alongside the Gaussian Process. The GP continued to provide uncertainty estimates and global guidance, while the neural network offered a more flexible approximation capable of capturing nonlinear or non-stationary behaviour that the GP might smooth over.

The neural network was not used as a standalone optimiser. Instead, it was employed as a comparative model, useful for ranking candidate points that had already passed a GP-based screening stage. This division of labour reduced the risk of overfitting while still extracting additional structure from the data.

Feature relevance became an explicit part of the methodology during this phase. GP kernel length scales were interpreted as indicators of dimension importance, while neural network input gradients provided local sensitivity estimates. Rather than removing dimensions outright, less influential features were implicitly down-weighted during candidate generation. This preserved the full input space while improving sampling efficiency and reducing noise from weak dimensions.

##Transition to Exploitation: Budget Awareness and Focused Search

Once a majority of the query budget had been consumed, the optimisation objective shifted again. At this point, continued broad exploration was unlikely to outperform focused refinement of already promising regions.

The strategy transitioned from structured exploration toward controlled exploitation. This transition was driven by explicit budget awareness rather than a fixed iteration count. Decisions were evaluated in terms of opportunity cost, with late-stage exploratory queries requiring stronger justification.

A two-stage candidate selection process was adopted more systematically. In the first stage, a relatively large pool of candidates was evaluated using the GP with a reduced exploration weight. In the second stage, a smaller shortlist was re-ranked using the neural network’s predicted mean. This approach preserved uncertainty awareness while increasingly favouring high-performing regions.

The interaction between the two models acted as a form of internal validation. Candidates favoured by both surrogates were treated as lower-risk exploitation steps, while disagreements prompted either conservative exploration or rejection, depending on remaining budget.

##Final Strategy: Controlled Exploitation and Local Refinement

In the final rounds, candidate generation was restricted to a local neighbourhood around the current best-known solution. The sampling radius was progressively reduced to concentrate evaluations where marginal gains were most likely.

Feature importance estimates continued to influence how tightly each dimension was sampled. More influential dimensions retained wider local variation, while weaker dimensions were narrowed more aggressively. This reflected an implicit dimensionality reduction without removing variables from the model.

Acquisition behaviour was deliberately made more exploitative by lowering uncertainty coefficients in the GP. This reflected increased confidence in the surrogate models and recognition that late-stage exploratory failures were unlikely to be recoverable within the remaining budget.

At this stage, the neural network played a decisive role in fine-grained ranking rather than exploration. Its role resembled that of a local optimiser operating within a region already validated by the GP. This mirrors practical optimisation workflows where global models hand off to local refiners near convergence.

##Hyperparameter Adaptation Across the Project

Throughout the thirteen weeks, hyperparameters were treated as adaptive controls rather than fixed design choices. Candidate pool sizes were gradually reduced, exploration weights were steadily lowered, and local sampling radii were introduced and tightened over time.

Neural network training parameters were also adjusted, with batch sizes reduced in later rounds to stabilise local decisions and avoid excessive smoothing. These changes were guided by observed surrogate behaviour, convergence diagnostics, and diminishing marginal improvements rather than predefined schedules.

This adaptive tuning reflects a key methodological principle of the project: optimisation under severe constraints benefits more from responsive adjustment than from rigid optimisation pipelines.

##Reflections, Assumptions, and Limitations

The methodology assumes that local smoothness exists near good solutions and that feature relevance inferred from limited data is meaningful. While the adaptive strategy reduces wasted queries and improves stability, it may still miss isolated global optima or underperform on highly deceptive landscapes.

These risks were mitigated through cautious transitions rather than abrupt shifts, and by retaining small amounts of exploration even in the final rounds. The resulting approach favours robustness, interpretability, and controlled convergence over aggressive optimisation, aligning with realistic constraints encountered in real-world ML and optimisation problems.
