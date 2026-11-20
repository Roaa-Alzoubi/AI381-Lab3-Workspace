# Lab 3 Reflection

## 1. What I learned

This lab helped me understand that an ML model is not ready for production just because it achieves a good evaluation score. A reliable ML workflow should track the training data, parameters, metrics, model artifacts, and the relationship between the model and the code used to build it.

I also learned how MLflow can make experiments easier to compare and how a promotion gate can prevent a model from being promoted when it does not meet predefined requirements.

## 2. Why I selected Logistic Regression Balanced

I compared Logistic Regression Balanced, Random Forest, and Gradient Boosting using the same dataset and train/test split.

I selected Logistic Regression Balanced because it achieved the highest overall F1 score (0.5890) and AUC (0.7975), while keeping inference latency low (7.66 ms) and model size small (3.55 KB).

However, this choice has a trade-off. Random Forest achieved a better worst-case contract-slice F1 score (0.4000 compared with 0.3333). The selected model therefore performs better overall, but its performance on two-year contracts needs further investigation.

## 3. What happened when the promotion gate blocked a model

I tested the promotion gate with a deliberately weak model on a separate branch.

The training and candidate evaluation steps completed, but the promotion gate failed with exit code 1. This demonstrated that the workflow can stop a model from being promoted when it does not satisfy the promotion requirements.

I kept the pull request open and did not merge the weak-model experiment into the main branch.

This taught me that automated gates are useful because they make model-quality requirements enforceable rather than relying only on manual review.

## 4. Deployment and lineage

I deployed the API locally using Docker Compose and verified it with the smoke test. The container reported a healthy status, and the API returned a valid prediction.

I also learned that lineage requires more than displaying a commit SHA in an API response. The Docker image, deployed application, Git commit, and MLflow training run should all be connected and consistent.

During verification, I found that the deployed application reported commit 806e325, while the existing MLflow run recorded an earlier source commit and a local Git SHA. This showed me why lineage metadata must be checked against the original training run rather than assumed to be correct.

## 5. What I would improve next

I would investigate the low F1 score for two-year contracts and evaluate the model on newer data to detect possible data drift.

I would also make the training workflow record the exact Git commit consistently in MLflow and the deployed image, so that every production model can be traced back to its training code, dataset, and experiment.

Overall, this lab taught me to treat model deployment as a controlled and traceable process, not simply as running an API.
