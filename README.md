# AutoRegress-Chemometrics

AutoRegress Framework
The AutoRegress framework implements an automated multi-stage pipeline for optimizing regression models on spectral data. The key stages are described below:
1. Phase 0: Broad Model Search
An initial assessment is performed by training a suite of regression models on the complete feature set. The models include Partial Least Squares Regression (PLSR), Ridge Regression, Lasso Regression, ElasticNet, Linear Support Vector Regression (LinearSVR), and kernelized Support Vector Regression (SVR with RBF kernel). Hyperparameters for each model are optimized using Bayesian optimization over 20 iterations. The model yielding the highest performance, based on R² from 5-fold cross-validation on the training data, is designated as the initial "Best Overall Model."
2. Phase 1: Coarse Feature Filtering
If the "Best Overall Model" from Phase 0 does not meet a predefined R² cutoff of 0.995, various filter-based feature selection techniques are applied. These include VarianceThreshold, SelectKBest (using scoring functions such as ANOVA F-value and mutual information), and ReliefF. Each filter is evaluated using a set of predefined ‘k’ values (e.g., top 10, 20, 50, or 100 features), and the "Best Overall Model" is updated if any model–filter combination improves performance.
3. Phase 2: Fine-Tuning the Filter Parameter 'k'
For filters that allow selection of the parameter 'k' (such as SelectKBest and ReliefF), and if Phase 1 yielded a promising filter-model combination that did not yet meet the R² cutoff, Bayesian optimization over 20 iterations is employed to fine-tune the optimal number of features. If this fine-tuning enhances performance, the "Best Overall Model" is accordingly updated.
4. Phase 3: Re-Tuning the Model on Selected Features
If feature selection in Phase 2 results in a subset (i.e., not all features are used) and the R² criterion remains unmet (or if this phase is configured to always run for potential refinement), the hyperparameters of the "Best Overall Model" are re-optimized using Bayesian optimization on the selected feature subset over 20 iterations. The model is updated if this re-tuning leads to further performance gains.
5. Finalization
The pipeline terminates when the R² cutoff is achieved at any stage or after the completion of Phase 3. The final "Best Overall Model," determined by its R² on an independent hold-out test set, is then reported. Throughout the pipeline, Bayesian optimization guides hyperparameter tuning based on performance from 5-fold cross-validation on the training data. Data scaling (standardization) is performed internally within training folds and applied consistently to corresponding test sets.
