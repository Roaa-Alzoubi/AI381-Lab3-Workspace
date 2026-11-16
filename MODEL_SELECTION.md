# Model selection

## The question
The model predicts whether a telecom customer is likely to churn.
A false negative may cause the company to miss a chance to retain a customer, while a false positive may waste retention resources.

## Candidates

| Candidate | Why I tried it | AUC | F1 | F1 per slice (lowest) | p95 latency (ms) | Size (KB) | MLflow run ID |
|---|---|---:|---:|---:|---:|---:|---|
| Logistic Regression | Fast, simple, and interpretable baseline | 0.798 | 0.495 | 0.2500 | 7.62 | 3.55 | 187030e55cdb44aa9b34fe63c191563e |
| Random Forest | To capture nonlinear patterns and feature interactions | 0.759 | 0.454 | 0.2222 | 24.38 | 3160.13 | 90a95aa862c44e44876a754958889ee5 |
| Gradient Boosting | To test a boosting-based alternative | 0.748 | 0.417 | 0.2222 | 6.44 | 141.68 | 9c64bab413fd4208a3914bb484940d6c |

## The champion
I selected Logistic Regression. It achieved the highest F1 (0.495) and AUC (0.798), while remaining fast (7.62 ms p95) and very small (3.55 KB). I accepted that its lowest slice F1 was 0.250, so performance across customer groups needs further investigation.

## My promotion rules
- Latency budget (p95): 25 ms, because the API should respond quickly. All three candidates met this limit in the recorded experiments.
- Minimum gain to replace the champion (F1 points): 0.02, to avoid promoting a model for a very small improvement that may not be meaningful.

## What I would check next
I would investigate the customer slices with the lowest F1 and check how many examples they contain. I would also evaluate the model on newer data to detect changes in customer behavior and data drift.
