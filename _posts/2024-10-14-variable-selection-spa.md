---
title: Successive Projections Algorithm (SPA)
date: 2024-10-14
permalink: /posts/2024/10/variable-selection-spa /
categories:
  - Data Analysis
tags:
  - Variable Selection
  - Data Analysis
  - Machine Learning
  - SPA Algorithm
  - High-Dimensional Data
  - Spectral Analysis
---
This post discusses the Successive Projections Algorithm (SPA), a variable selection technique used primarily to identify linearly independent variables.

#### Successive Projections Algorithm
The concept behind SPA is straightforward. The algorithm works by iteratively selecting variables that are the most independent from those previously chosen. The steps are as follows:
1. Begin by selecting an initial variable.
2. Project all remaining variables onto the orthogonal subspace of the selected variables.
3. Choose the variable that is farthest from this subspace (i.e., most independent).
4. Repeat until the desired number of variables is selected.

#### QR Decomposition for SPA
An efficient alternative for implementing SPA is to use **QR decomposition with column pivoting**, which offers a computational advantage, especially for large datasets. QR decomposition is a matrix factorization technique that breaks a matrix into two components:
- `Q` (an orthogonal matrix) and 
- `R` (an upper triangular matrix).
  
Using QR decomposition with column pivoting allows for a reordering of columns based on their linear independence. This approach aligns with SPA’s goal of selecting the most independent variables, providing a faster and computationally optimized alternative.

#### Benefits of QR Decomposition for SPA
- Efficiency: Handles large datasets more efficiently than iterative projection.
- Simplicity: Column pivoting offers a straightforward way to rank variables by their importance.
- Flexibility: It approximates SPA’s selection process, significantly reducing execution time.

#### Implementing SPA Using QR Decomposition

Below is a step-by-step guide to implementing SPA using the built-in QR decomposition function in MATLAB.

```matlab
function chain = projections_qr(X, k, M)
    % Projections routine for Successive Projections Algorithm using QR decomposition
    % Inputs: 
    % X --> Predictor matrix (N x K), 
    % k --> Initial column index,
    % M --> Number of variables to select.
    % Output: chain --> Index set of selected variables
    
    X_projected = X;
    norms = sum(X_projected.^2);
    norm_max = max(norms);
    X_projected(:, k) = X_projected(:, k) * 2 * norm_max / norms(k);
    
    [~, ~, order] = qr(X_projected, 0);
    chain = order(1:M)';
end
```
#### Classical SPA Implementation
Now, let’s compare this approach with the traditional SPA implementation. Note that the following code can be further optimized, for example, by avoiding the computation of the inverse matrix:
```matlab
function Idx = spa_classic(X, k, M)
    % Classic Successive Projections Algorithm (SPA)
    % Inputs:
    % X --> Data matrix (N x K), 
    % k --> Initial column index,
    % M --> Number of variables to select.
    % Output: Idx --> Selected variable indices
    
    [N, K] = size(X);
    Idx = zeros(1, M);
    Idx(1) = k;
    X_selected = X(:, k);
    
    for i = 2:M
        P = eye(N) - (X_selected / (X_selected' * X_selected)) * X_selected';
        X_projected = P * X;
        norms = sum(X_projected.^2);
        norms(Idx(1:i-1)) = 0;
        [~, new_selection] = max(norms);
        Idx(i) = new_selection;
        X_selected = [X_selected, X(:, new_selection)];
    end
end
```
Now that we have both methods, we can compare their running times with the following test script:
```matlab
clc;
clear;

% N = number of observations, K = number of variables (predictors)
N = 100;  % Number of observations (rows)
K = 7;    % Number of predictor variables (columns)

% Generate a synthetic dataset with correlated and uncorrelated variables
x1 = randn(N, 1)*10;
x2 = 2 * x1 + 0.01 * randn(N, 1);
x3 = 5 * x1 + 0.01 * randn(N, 1);
x4 = 7 * x1 + 0.01 * randn(N, 1);

% Combine correlated and uncorrelated variables
X = [x1, randn(N, 1)*10, x2, x3, randn(N, 1)*10, x4, randn(N, 1)*10];

% Select starting variable and number of variables to pick
k = 1;   % Initial variable index
M = 4;   % Number of variables to select

% Measure time for spa_classic
tic;  
Idx_classic = spa_classic(X, k, M);
time_classic = toc;

% Measure time for spa_qr
tic;
Idx_qr = projections_qr(X, k, M);
time_qr = toc;

% Display results
disp('Selected variable indices by Classic SPA:');
disp(Idx_classic);
fprintf('Time taken by Classic SPA: %.6f seconds\n', time_classic);

disp('Selected variable indices by QR-based SPA:');
disp(Idx_qr);
fprintf('Time taken by QR-based SPA: %.6f seconds\n', time_qr);

% Compare times
if time_classic < time_qr
    fprintf('Classic SPA is faster by %.6f seconds\n', time_qr - time_classic);
else
    fprintf('QR-based SPA is faster by %.6f seconds\n', time_classic - time_qr);
end
```
#### Results
Here’s an example of the output for a dataset with $N = 100$ observations and $K = 7$ predictor variables:
```matlab
Selected variable indices by classic spa:
     1     7     2     5

Time taken by spa_classic: 0.000430 seconds
Selected variable indices by spa_qr:
     1     7     2     5

Time taken by spa_qr: 0.000067 seconds
spa_qr is faster by 0.000363 seconds
```
##### Comparison: Classical SPA vs. QR-based SPA

|                     | **Classical SPA**                                      | **QR-based SPA**                                 |
|---------------------------------|-------------------------------------------------------|--------------------------------------------------|
| **Projection Method**           | Iteratively projects variables onto orthogonal subspaces | Uses QR decomposition to reorder variables       |
| **Variable Selection**          | Selects variables one at a time                       | Selects variables in one step based on column pivoting |
| **Computational Efficiency**    | Less efficient for large datasets due to iterative projections | More efficient for large datasets due to QR optimization |
| **Starting Variable**           | Begins with a user-selected variable                  | Begins with a scaled version of the user-selected variable |
| **Main Goal**                   | Maximize linear independence between variables        | Maximize linear independence between variables   |

#### Conclusion
In this post, we presented both the traditional and QR-based SPA methods for variable selection. While both methods achieve the goal of selecting independent variables, the QR-based method offers improved efficiency, particularly for large datasets. By leveraging MATLAB’s QR decomposition function, you can achieve faster results while maintaining the robustness of SPA.

#### Further Reading:
- For more details on QR decomposition, check out [MATLAB’s QR documentation](https://www.mathworks.com/help/matlab/ref/qr.html).
- Learn more about the classical **Successive Projections Algorithm (SPA)** in the context of spectral analysis [here](https://www.sciencedirect.com/science/article/abs/pii/S0169743901001198).


