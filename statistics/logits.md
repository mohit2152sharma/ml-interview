# Logit

1. What is logit?

- logit is logarithm of odds.
- $$\text{logit}(p) = \log\left(\frac{p}{1-p}\right)$$
- odds is given by $\frac{p}{1-p}$, where $p$ is the probability of event occurring. It tells how likely an event is to happen compared to it not happening.
- logit function maps the probabilities from $[0, 1]$ to $(-\inf, \inf)$. It is the inverse of logistic (sigmoid) function.
- Logistic regression models the log odds of a positive event as a linear function.
