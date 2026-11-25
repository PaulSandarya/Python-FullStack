# Machine Learning Technical Interview Prep Guide

A study guide with a preparation plan, the questions interviewers actually ask, and concise model answers.

## 1. How to Use This Guide

ML interviews usually test five things: **fundamentals** (stats, bias-variance, metrics), **classical algorithms**, **deep learning**, **practical judgment** (data problems, debugging, system design), and **coding**. For each question below, try answering out loud first, then compare with the model answer. If you cannot explain something simply, revisit it.

**Suggested 4-week plan**

- **Week 1: Foundations.** Probability, statistics, linear algebra, loss functions, bias-variance, regularization, evaluation metrics.
- **Week 2: Algorithms.** Linear and logistic regression, trees, random forests, boosting, SVM, kNN, k-means, PCA. Implement 3 from scratch with NumPy.
- **Week 3: Deep learning and modern ML.** Backprop, optimizers, normalization, CNNs, RNNs, attention and transformers, fine-tuning, RAG.
- **Week 4: Applied.** ML system design, experiment design, debugging models, mock interviews, project stories.

**Rules of thumb for answering**

1. Start with a one-line definition, then intuition, then trade-offs.
2. Give a concrete example from a project or dataset.
3. State assumptions before diving in, especially in design questions.
4. When unsure, reason from first principles aloud instead of guessing.

## 2. Math and Statistics Fundamentals

**Q1. Explain bias and variance. What is the trade-off?**
Bias is error from overly simple assumptions (underfitting). Variance is error from sensitivity to the training sample (overfitting). Expected test error decomposes into bias squared + variance + irreducible noise. Increasing model complexity lowers bias but raises variance. The goal is the complexity that minimizes total error, found via validation.

**Q2. What is the difference between MLE and MAP?**
Maximum likelihood picks parameters that maximize P(data | θ). MAP maximizes P(θ | data), which adds a prior. A Gaussian prior on weights gives L2 regularization; a Laplace prior gives L1.

**Q3. What is Bayes' theorem and where is it used in ML?**
P(A|B) = P(B|A)P(A) / P(B). It underlies Naive Bayes, Bayesian inference, MAP estimation, and probabilistic calibration.

**Q4. What does the Central Limit Theorem say and why does it matter?**
The distribution of the sample mean approaches a normal distribution as sample size grows, regardless of the underlying distribution (given finite variance). It justifies confidence intervals and many hypothesis tests, such as A/B test analysis.

**Q5. Explain p-values and confidence intervals.**
A p-value is the probability of seeing data at least this extreme if the null hypothesis were true. It is not the probability the null is true. A 95% confidence interval is a procedure that, over repeated sampling, captures the true parameter 95% of the time.

**Q6. Type I vs Type II errors?**
Type I is a false positive (rejecting a true null). Type II is a false negative (failing to reject a false null). Power = 1 − P(Type II).

**Q7. Why do eigenvalues and eigenvectors matter in ML?**
Eigenvectors of the covariance matrix give the principal components in PCA; eigenvalues give the variance along each. They also appear in spectral clustering, stability analysis of optimization, and understanding Hessian curvature.

**Q8. What is the curse of dimensionality?**
As dimensions grow, data becomes sparse, distances become less discriminative, and the amount of data needed to generalize grows rapidly. Distance-based methods like kNN suffer most. Remedies: feature selection, dimensionality reduction, regularization.

**Q9. Correlation vs covariance vs causation?**
Covariance measures joint variability and depends on scale. Correlation is normalized covariance in [−1, 1] and captures only linear association. Neither implies causation; you need randomized experiments or causal inference methods.

## 3. Core ML Concepts

**Q10. What is overfitting and how do you prevent it?**
The model fits noise in the training data and generalizes poorly. Remedies: more data, simpler models, L1/L2 regularization, dropout, early stopping, data augmentation, cross-validation, ensembling, pruning (trees).

**Q11. L1 vs L2 regularization?**
L1 (lasso) adds the sum of absolute weights and drives some weights to exactly zero, giving sparsity and feature selection. L2 (ridge) adds the sum of squared weights and shrinks weights smoothly, handling correlated features better. Elastic net combines both.

**Q12. How does cross-validation work and when does it fail?**
K-fold splits data into k parts, trains on k−1, validates on the remaining one, and averages the results. It fails with time series (use forward-chaining splits), grouped data such as multiple rows per user (use group k-fold), and when preprocessing is fit before splitting (leakage).

**Q13. What is data leakage? Give examples.**
Information from outside the training set, or from the future, leaks into features. Examples: fitting a scaler on the full dataset, using a feature computed after the prediction time, duplicate records across train and test, target-derived features. Symptoms: suspiciously high validation scores that collapse in production.

**Q14. Which metrics would you use for classification?**

- **Accuracy**: fine only when classes are balanced.
- **Precision**: of predicted positives, how many are right.
- **Recall**: of actual positives, how many we found.
- **F1**: harmonic mean of precision and recall.
- **ROC-AUC**: ranking quality across thresholds; can look optimistic on heavy imbalance.
- **PR-AUC**: preferred for rare positives.
- **Log loss / calibration**: when probabilities matter.

Choose by business cost: fraud and disease screening favor recall; spam filtering for important mail favors precision.

**Q15. How do you handle class imbalance?**
Use appropriate metrics (PR-AUC, recall at fixed precision), class weights or focal loss, resampling (SMOTE, undersampling) applied only to the training fold, threshold tuning, and anomaly-detection framing for extreme cases. Collecting more minority data is often best.

**Q16. Regression metrics?**
MSE and RMSE penalize large errors; MAE is robust to outliers; R² measures variance explained; MAPE is scale-free but unstable near zero. Pick the one aligned with the business cost.

**Q17. Generative vs discriminative models?**
Discriminative models learn P(y|x) directly (logistic regression, SVM, neural nets). Generative models learn P(x, y) or P(x|y) (Naive Bayes, GMMs, GANs, diffusion) and can generate data, but classification accuracy is often lower with limited data.

**Q18. What is calibration?**
A model is calibrated if predicted probability matches observed frequency (of items scored 0.8, about 80% are positive). Check with reliability diagrams; fix with Platt scaling or isotonic regression.

## 4. Classical Algorithms

**Q19. Explain linear regression assumptions.**
Linearity, independent errors, constant variance (homoscedasticity), normally distributed errors (for inference), and no severe multicollinearity. Violations affect inference more than prediction.

**Q20. Why is logistic regression a linear model, and how is it trained?**
It models log-odds as a linear function of features: log(p/(1−p)) = w·x + b. The decision boundary is linear. It is trained by minimizing cross-entropy (equivalent to MLE), typically with gradient descent or L-BFGS. The loss is convex.

**Q21. How does a decision tree split?**
At each node it chooses the feature and threshold that maximize impurity reduction: Gini or entropy (information gain) for classification, variance reduction for regression. Trees are interpretable and handle mixed types but overfit without depth limits or pruning.

**Q22. Random forest vs gradient boosting?**
Random forests train many deep trees independently on bootstrap samples with random feature subsets, then average; this mainly reduces variance. Gradient boosting trains shallow trees sequentially, each fitting the residual (gradient of the loss) of the ensemble; this mainly reduces bias. Boosting usually gets higher accuracy but needs more tuning (learning rate, depth, number of trees) and overfits more easily.

**Q23. How do XGBoost, LightGBM and CatBoost differ?**
XGBoost uses regularized objectives and second-order gradients with level-wise growth. LightGBM uses histogram binning and leaf-wise growth, making it fast on large data. CatBoost handles categorical features natively with ordered target statistics to reduce leakage.

**Q24. Explain SVMs and the kernel trick.**
SVMs find the hyperplane maximizing the margin between classes; only support vectors define it. The kernel trick computes inner products in a high-dimensional feature space without explicitly mapping, enabling nonlinear boundaries (RBF, polynomial). Hyperparameter C trades margin width against misclassification.

**Q25. How does kNN work and what are its weaknesses?**
It predicts by majority vote or average of the k nearest training points. Weaknesses: slow at inference, sensitive to feature scaling and irrelevant features, and degrades in high dimensions. Choose k by validation.

**Q26. Explain k-means and how to choose k.**
It alternates assigning points to the nearest centroid and recomputing centroids until convergence, minimizing within-cluster variance. Choose k with the elbow method, silhouette score, or domain needs. Limitations: assumes spherical clusters of similar size, sensitive to initialization (use k-means++) and outliers.

**Q27. Explain PCA.**
PCA finds orthogonal directions of maximum variance via eigendecomposition of the covariance matrix (or SVD). Projecting onto the top components reduces dimensionality while keeping most variance. Standardize features first. It is linear and unsupervised, so it may discard class-relevant low-variance directions.

**Q28. Naive Bayes: why "naive" and why does it still work?**
It assumes features are conditionally independent given the class. This is rarely true, but the classifier only needs correct ranking of class probabilities, and the model has low variance, which helps on small or high-dimensional data like text.

**Q29. Bagging vs boosting vs stacking?**
Bagging averages models trained on bootstrap samples (variance reduction). Boosting sequentially corrects errors (bias reduction). Stacking trains a meta-model on the predictions of diverse base models, using out-of-fold predictions to avoid leakage.

## 5. Deep Learning

**Q30. Explain backpropagation.**
It applies the chain rule to compute the gradient of the loss with respect to every parameter by propagating error signals backward through the computation graph. Cost is roughly the same as one forward pass. Frameworks automate this via autodiff.

**Q31. What are vanishing and exploding gradients?**
In deep networks, repeated multiplication of Jacobians can shrink gradients toward zero or blow them up. Fixes: ReLU-family activations, careful initialization (He/Xavier), residual connections, normalization layers, gradient clipping (for explosion), and gated architectures (LSTM/GRU).

**Q32. SGD, momentum, RMSProp, Adam?**
SGD follows minibatch gradients. Momentum accumulates a velocity to smooth noise and speed progress along consistent directions. RMSProp scales updates by a running average of squared gradients. Adam combines momentum and RMSProp with bias correction. AdamW decouples weight decay and is the default for transformers. SGD with momentum often generalizes well on vision tasks.

**Q33. Batch norm vs layer norm?**
Batch norm normalizes across the batch per feature; it depends on batch statistics and behaves differently at train and inference time. Layer norm normalizes across features within each sample, independent of batch size, which is why transformers and RNNs use it.

**Q34. Why use dropout and how does it behave at inference?**
Dropout randomly zeroes activations during training, discouraging co-adaptation and acting like an implicit ensemble. At inference all units are active, with activations scaled appropriately (inverted dropout scales during training).

**Q35. Common activation functions?**
ReLU is simple and avoids saturation but can "die"; Leaky ReLU and GELU mitigate this; sigmoid and tanh saturate and are mainly used for gates or outputs; softmax converts logits to class probabilities.

**Q36. How do CNNs work and why do they suit images?**
Convolutions apply shared local filters, giving translation equivariance and far fewer parameters than dense layers. Stacked layers learn hierarchical features from edges to objects. Pooling or strides downsample; residual connections (ResNet) enable very deep nets.

**Q37. Explain attention and the transformer.**
Self-attention computes, for each token, a weighted sum of value vectors where weights come from softmax(QKᵀ/√d). Multi-head attention learns multiple relation types in parallel. Transformers stack attention and feed-forward blocks with residuals and layer norm, use positional information, and parallelize across the sequence, unlike RNNs. Attention cost is O(n²) in sequence length.

**Q38. Why divide by √d in attention?**
Dot products grow with dimension d, pushing softmax into saturated regions with tiny gradients. Scaling keeps variance near 1.

**Q39. Training loss goes down but validation loss goes up. What do you do?**
That is overfitting. Add regularization, data augmentation, or more data; reduce capacity; apply early stopping; check for train/validation distribution mismatch or leakage.

**Q40. Your model's loss is NaN or not decreasing. Debugging checklist?**
Check data (labels, NaNs, normalization), overfit a tiny batch first, verify loss and output shapes, lower the learning rate, add gradient clipping, check initialization, and inspect gradient norms and activations layer by layer.

## 6. Modern ML and LLMs

**Q41. Pretraining vs fine-tuning vs prompting?**
Pretraining learns general representations from large corpora. Fine-tuning adapts weights to a task or domain. Prompting steers a frozen model with instructions or examples. Try prompting first, then RAG, then fine-tuning when you need consistent style, format, or domain behavior.

**Q42. What is LoRA?**
Low-Rank Adaptation freezes the base weights and trains small low-rank matrices added to selected layers. It cuts trainable parameters and memory dramatically and allows swapping adapters per task.

**Q43. What is RAG and when is it better than fine-tuning?**
Retrieval-augmented generation retrieves relevant documents (via embeddings and a vector index) and puts them in the prompt. It suits knowledge that changes often, needs citations, or is too large to bake into weights. Key quality levers: chunking, embedding choice, hybrid search, reranking, and evaluation of retrieval separately from generation.

**Q44. How do you evaluate an LLM application?**
Build a representative test set, define task-specific rubrics, combine automated checks (exact match, unit tests, schema validation), LLM-as-judge with calibration against human labels, and online metrics. Track hallucination rate, latency, and cost. Regression-test on every prompt or model change.

**Q45. What causes hallucination and how can you reduce it?**
Models optimize plausible continuation, not truth. Mitigate with grounding via retrieval, constrained output formats, asking the model to cite or abstain, verification steps, and better evaluation.

**Q46. What is RLHF or preference optimization?**
A model is tuned using human (or AI) preference comparisons, either via a reward model plus RL (PPO) or directly via methods like DPO, to align outputs with desired behavior.

## 7. Data, Features, and Experimentation

**Q47. How do you handle missing values?**
First understand why data is missing (MCAR, MAR, MNAR). Options: drop, impute (mean, median, model-based), or add a missing-indicator feature. Tree models like LightGBM handle missing values natively. Always fit imputers on training data only.

**Q48. Encoding categorical variables?**
One-hot for low cardinality; ordinal for true order; target or frequency encoding and embeddings for high cardinality (with out-of-fold computation to avoid leakage); hashing for huge vocabularies.

**Q49. Feature scaling: when is it needed?**
Needed for distance-based and gradient-based models (kNN, SVM, neural nets, regularized linear models, PCA). Not needed for tree-based models.

**Q50. How would you design and analyze an A/B test?**
Define the metric and hypothesis, pick randomization unit, compute sample size from baseline, minimum detectable effect, power, and alpha, run for full business cycles, check sample ratio mismatch, then test. Beware peeking, multiple comparisons, novelty effects, and interference between users.

**Q51. Offline metric improved but online metric did not. Why?**
Possible causes: train/serve skew, distribution shift, offline metric misaligned with business goal, feedback loops, position bias, leakage in offline evaluation, or insufficient experiment power.

**Q52. How do you detect and handle drift?**
Monitor input feature distributions (PSI, KS test), prediction distributions, and, when labels arrive, performance. Distinguish data drift (inputs change), concept drift (relationship changes), and label shift. Responses: retrain on a schedule or trigger, use recent-weighted data, or redesign features.

## 8. ML System Design

Use this framework for any design question (recommender, fraud detection, search ranking, ad CTR):

1. **Clarify the goal**: business objective, users, scale, latency, constraints.
2. **Define the ML task and metrics**: offline metrics and online/business metrics, plus guardrails.
3. **Data**: sources, labels (explicit or implicit), freshness, biases, privacy.
4. **Features**: user, item, context, and interaction features; real-time vs batch.
5. **Model**: start with a simple baseline, then a stronger model; justify trade-offs. Consider multi-stage designs (candidate generation, ranking, re-ranking).
6. **Training and evaluation**: splits that respect time, negative sampling, calibration.
7. **Serving**: latency budget, caching, feature store, batch vs online inference, fallbacks.
8. **Monitoring and iteration**: drift, A/B testing, retraining cadence, feedback loops.

**Practice prompts**

- Design a recommendation system for a video platform.
- Build a fraud detection system for payments with 0.1% fraud rate.
- Design a search ranking system for an e-commerce site.
- Build a spam or toxicity classifier with changing adversaries.
- Design a customer support chatbot using an LLM with company documentation.

## 9. Coding Practice

Expect to code in Python with NumPy, and sometimes pandas or PyTorch.

**Implement from scratch**

- Linear regression with gradient descent, then closed form.
- Logistic regression with cross-entropy.
- k-means and kNN.
- Decision tree split finder (Gini).
- Softmax and cross-entropy with a numerically stable implementation.
- A small MLP with manual backprop.
- Scaled dot-product attention.
- Metrics: precision, recall, F1, AUC.

**Pandas and SQL themes**

- Group-by aggregations, window functions, joins, and deduplication.
- Rolling features without leakage.
- Top-N per group and retention or cohort calculations.

**Classic algorithm questions** (often asked too): arrays and hashing, two pointers, heaps (top-k), binary search, basic dynamic programming, and graph traversal.

**Example: stable softmax**

```python
import numpy as np

def softmax(z):
    z = z - np.max(z, axis=-1, keepdims=True)  # prevents overflow
    e = np.exp(z)
    return e / e.sum(axis=-1, keepdims=True)
```

**Example: scaled dot-product attention**

```python
def attention(Q, K, V, mask=None):
    d = Q.shape[-1]
    scores = Q @ K.swapaxes(-1, -2) / np.sqrt(d)
    if mask is not None:
        scores = np.where(mask, scores, -1e9)
    return softmax(scores) @ V
```

## 10. Project and Behavioral Questions

Interviewers probe your own projects deeply. Prepare a 2-minute story for each major project using this structure: **problem, data, approach, evaluation, result, what you would change.**

Likely follow-ups:

- Why did you choose this model over alternatives?
- How did you validate it, and what could be wrong with that validation?
- What was the hardest bug or data issue?
- How did the model impact the business or users?
- What would you do with 10x more data or 10x less latency?
- Tell me about a time a model failed in production.

## 11. Rapid-Fire Review

| Topic | One-line reminder |
|---|---|
| Bias-variance | Complexity trades bias for variance; tune on validation data |
| L1 vs L2 | L1 sparsifies, L2 shrinks |
| Imbalance | Use PR-AUC, weights, threshold tuning |
| Leakage | Fit all preprocessing inside the training fold |
| Bagging vs boosting | Variance reduction vs bias reduction |
| Adam vs SGD | Adam adapts per-parameter; SGD often generalizes well |
| BatchNorm vs LayerNorm | Batch statistics vs per-sample statistics |
| Attention cost | O(n²) in sequence length |
| RAG vs fine-tune | Knowledge and freshness vs behavior and style |
| A/B test | Power, SRM check, no peeking |

## 12. Final Tips

- Know your resume: every algorithm or tool listed is fair game.
- Practice explaining concepts to a non-expert; clarity signals depth.
- Say "I would check X because Y" rather than reciting definitions.
- In coding rounds, talk through the plan, write a simple correct solution first, then optimize.
- Review recent papers or tools relevant to the role, but prioritize fundamentals.
- Do at least 3 timed mock interviews before the real one.
