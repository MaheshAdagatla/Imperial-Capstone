Methodology.md
1. Purpose of This Document

This document explains the reasoning and decision-making behind the methods used in the Black-Box Optimisation (BBO) Capstone Project. It complements the README.md by describing why particular modelling choices, acquisition functions, and workflows were adopted. The aim is to ensure that peers, facilitators, and future collaborators can trace the logic behind each design decision and evaluate the project’s reproducibility.

2. Problem Understanding

The challenge is to optimise unknown, expensive-to-evaluate functions with a strict query limit. Each function can only be probed a limited number of times, and the goal is to find the maximum using as few evaluations as possible. Because the underlying structure of the function is unknown and potentially noisy, the project focuses on sequential, data-efficient learning. Every evaluation must yield high information gain, the model must provide accurate uncertainty estimates, and the optimisation loop must adapt dynamically as new data arrive.

3. Modelling Rationale
Gaussian Process (GP) Surrogate

Gaussian Processes are the main surrogate model because they provide both predictive means and uncertainty estimates, allowing the optimisation algorithm to balance exploration and exploitation. The Matern kernel is used since it can represent a wide range of smooth and non-smooth surfaces, making it robust for unknown functions. Hyperparameters such as the length scale, kernel variance, and noise level are re-optimised after each iteration. The length-scale bounds are particularly useful for detecting irrelevant input dimensions through automatic relevance determination. GPs are most suitable for low-dimensional tasks with fewer than about 40 observations, where computational cost is manageable.

Neural Network Surrogate

Neural networks are used as an alternative surrogate when the function’s dimensionality increases or when the GP shows signs of underfitting. The network is implemented in PyTorch with two to three hidden layers and between 32 and 128 neurons. Activation functions such as ReLU or Tanh are chosen based on convergence behaviour. The main reason for including a neural network is its flexibility in modelling highly non-stationary or irregular response surfaces. Gradient-based sensitivity analysis is used to identify influential features, which can guide future sampling decisions.

4. Acquisition Function Selection

Expected Improvement (EI) is used as the acquisition function because it naturally balances exploration and exploitation. It quantifies the expected gain in function value relative to the current best observation, while also accounting for uncertainty in the model’s predictions. In early iterations, a higher exploration parameter (xi between 0.01 and 0.05) encourages sampling in diverse regions to avoid missing global optima. As the optimisation progresses, xi is reduced to around 0.001 to focus more on refining the best region found so far. Alternative acquisition strategies such as Probability of Improvement (PI) and Upper Confidence Bound (UCB) were considered, but EI was preferred because PI tends to overexploit early, and UCB can overexplore under limited data conditions.

5. Sequential Optimisation Workflow

Each iteration of the optimisation process follows a structured loop.
First, the initial data are prepared by loading the existing input-output pairs and applying normalisation to ensure numerical stability.
Next, the surrogate model (either Gaussian Process or Neural Network) is trained to approximate the current belief about the function.
After fitting, the acquisition function is evaluated over candidate input points to identify the next query that offers the best trade-off between potential improvement and uncertainty.
This selected point is then evaluated on the true black-box function, and the resulting output value is added to the dataset.
The model is retrained with this new data, updating its parameters and posterior beliefs.
Throughout this process, the model’s convergence is monitored through visual plots and numerical summaries to check whether Expected Improvement values are stabilising.
Feature sensitivity and uncertainty estimates are also reviewed after each iteration to decide whether exploration or exploitation should be prioritised in the next step.

6. Hyperparameter Optimisation Justification

For Gaussian Processes, kernel hyperparameters are re-estimated via maximum marginal likelihood at every iteration. This dynamic adjustment helps the model remain flexible as more data become available. Length scales also reveal which features have the greatest influence, allowing irrelevant dimensions to be implicitly down-weighted.
For Neural Networks, hyperparameters such as the number of hidden layers, neurons, learning rate, and batch size are selected empirically through short trial runs. The goal is to find the smallest network that maintains stability and accuracy without overfitting.
The exploration parameter xi in the acquisition function is tuned dynamically across iterations. It begins with a large value to promote exploration and gradually decreases as convergence is approached.
The optimisation process can stop when the Expected Improvement values plateau or when additional evaluations no longer lead to significant improvement, typically defined as less than a 1% gain.

7. Feature Sensitivity and Dimensionality Handling

Feature importance is assessed differently for each model type. In the Gaussian Process, the inverse of the learned length scale serves as an indicator of how sensitive the output is to changes in a particular input dimension. In the Neural Network, gradients of the output with respect to each input are used to identify the most influential features. By focusing on features with high sensitivity and deprioritising less important ones, the model becomes more efficient and converges faster with limited data. This helps allocate the small number of available queries to regions of the input space that are most likely to yield improvement.
