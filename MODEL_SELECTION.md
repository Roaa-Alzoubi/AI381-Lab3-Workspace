# Model selection

## The question

The model predicts whether a telecom customer is likely to churn.
A false negative may cause the company to miss a chance to retain a customer, while a false positive may waste retention resources.

## Experiment setup

All three candidates were trained and evaluated using the same dataset (`data/churn_v1.csv`) and the same stratified train/test split (`test_size=0.2`, `random_state=42`).

Data SHA-256 prefix: `76a6447e764b`

The experiment batch is `balanced-v1`.

## Candidates

| Candidate | Why I tried it | AUC | F1 | Lowest slice F1 | p95 latency (ms) | Size (KB) | MLflow run ID |
|---|---|---:|---:|---:|---:|---:|---|
| Logistic Regression Balanced | Class weighting helps address the class imbalance while keeping the model simple and interpretable | 0.7975 | 0.5890 | 0.3333 | 7.66 | 3.55 | c0e9ae111612423b97810ff66e5a50e5 |
| Random Forest 50 | Tests nonlinear patterns and feature interactions with a limited number of trees | 0.7467 | 0.5312 | 0.4000 | 9.00 | 795.61 | e8da7efdb3034ed0ae59d1137af85d89 |
| Gradient Boosting 100 | Tests a boosting-based alternative with relatively low inference latency | 0.7637 | 0.4000 | 0.2222 | 6.85 | 142.52 | da2d7b9a9b9f41ef9622e3bcac5f3a70 |

## F1 by contract slice

| Candidate | Month-to-month | One-year | Two-year |
|---|---:|---:|---:|
| Logistic Regression Balanced | 0.6260 | 0.5714 | 0.3333 |
| Random Forest 50 | 0.5524 | 0.4000 | 0.4615 |
| Gradient Boosting 100 | 0.4384 | 0.2500 | 0.2222 |

Random Forest achieved the strongest worst-case slice F1 (0.4000), while Logistic Regression Balanced achieved the best overall F1 and AUC. The two-year contract slice remains an area for investigation for the selected model.

## The champion

I selected Logistic Regression Balanced as the champion.

It achieved the highest overall F1 (0.5890) and AUC (0.7975). Its p95 latency was only 7.66 ms, comfortably below the 25 ms budget, and its model size was just 3.55 KB.

Random Forest had a better lowest-slice F1, but its model was much larger (795.61 KB) and its overall F1 was lower (0.5312). Gradient Boosting had the lowest overall F1 (0.4000).

The previous champion was Logistic Regression with F1 0.495. The new champion improved F1 by 0.094, exceeding the required minimum gain of 0.02. MLflow registered the new model as version 2 and assigned it the `champion` alias.

I accept the lower F1 on the two-year contract slice (0.3333) for this iteration, but it should be investigated before relying on the model for high-impact retention decisions.

## My promotion rules

- Latency budget (p95): 25 ms, because the API should respond quickly. All three candidates met this limit in this experiment.
- Minimum gain to replace the champion (F1 points): 0.02, to avoid promoting a model for a very small improvement that may not be meaningful.
- The promotion gate selects the best candidate within the latency budget and blocks promotion unless it improves on the current champion by at least 0.02 F1.

## What I would check next

I would investigate the two-year contract slice and check how many examples it contains, since its F1 is lower than the other contract groups. I would also evaluate the model on newer data to detect changes in customer behavior and data drift.
