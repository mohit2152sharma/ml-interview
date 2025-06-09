# Evaluation Metrics:

## Precision

- Used for #classification task or #retrieval task.
- It's a fraction of relevant instances among all the retrieved instances.
- It's also known as positive predictive value.
- $$Precision = \frac{TP}{TP + FP}$$
- For a spam filter, you would want high #precision i.e. low $False Positive$, meaning less counts of where you label an email as spam (FP) when actually it wasn't.

## Recall

- #recall is again used for #classification task or #retrieval task.
- It's a fraction of relevant instances that were retrieved.
- It's also known as #sensitivity
- $$Recall = \frac{TP}{TP + FN}$$
- For cancer detection system, you would want high #recall i.e. low $False Negative$, meaning you wouldn't want to miss out on a patient who actually has a cancer.

## F1 score

- It's a harmonic mean of #precision and #recall.
- It's useful to combine both #precision and #recall into a single score, especially with unbalanced classes.
- $$F1 = 2 \times \frac{(Precision \times Recall)}{(Precision + Recall)}$$
