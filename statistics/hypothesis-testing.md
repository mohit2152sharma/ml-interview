# Hypothesis Testing
It tests whether the results of a research are occurring by chance or whether the result are too extreme to be considered a chance variation. It uses sample data to evaluate a #hypothesis about the population. 
- Null Hypothesis: This is the opposite of alternate #hypothesis , and expresses that there is no relationship between variables or groups. 
- Alternate Hypothesis: This is the hypothesis that you propose, also known as research #hypothesis. 
The purpose of #hypothesis testing is to test whether the null hypothesis (there is no relationship, effect) can be rejected or approved. If the null hypothesis is rejected than the alternate hypothesis can be accepted. If the null hypothesis is accepted than the alternate hypothesis is rejected. 

### Steps for hypothesis testing 
- There are two population groups of interest. A alternate hypothesis could be something like there is significant difference between two populations while null hypothesis could be something like there is no difference between two populations. 
- Calculate the characteristics of the populations like mean, standard deviation. 
- Conduct the statistical test and evaluate result using #p-value and #alpha 
	- If  $p-value > \alpha$, failed to reject $H_0$, not enough evidence
	- If  $p-value <= \alpha$, reject $H_0$, result is statistically significant

### Assumptions made for [[hypothesis-testing]] 
- Random sampling: The data should be collected using a process that ensures that every member of population is equally likely to be included in the sample data. 
- Independence of observations: Each observation should be independent of others. That is the outcome of one data point should not affect the outcome of others. 
- Correct distribution: the data should follow the distribution assumed by the test. 
	- #t-test and #anova assume normality of data 
	- #chi-square assume categorical data and a sufficiently large sample for expected frequencies. 
- Homogeneity of #variance: when comparing two groups the variance within the group should be roughly equal and should not change (this is called #homoscedasticity)
- Scale of measurement: The type of data must match the type required by the tests. 
	- Interval or ratio for #t-test  and #anova 
	- Nominal or ordinal for #chi-square 
	- Binary outcomes for #logistic-regression tests
- Sufficient sample size: Most statistical tests assume a reasonably large sample size to ensure that sampling distributions are approximately normal. 
- No outliers or skewers are present in the sample dataset. 
- For #time-series or longitudinal data, one must check for autocorrelation or stationarity. 
- For #regression tests assumptions like linearity, no multicollinearity and normally distributed residuals are required. 

### Tests for normality 
These tests check whether the sample data comes from a normal distribution. The null hypothesis for these tests is "data comes from a normal distribution" and alternate hypothesis is that the "data does not come from a normal distribution". 
- Shapiro-wilk test 
- D'Agostino-pearson test 
- Anderson-darling test 
- Kolmogorov-smirnov test 
- Lilliefors (KS variant)
- Jarque-Bera test 
- Visual inspection with Q-Q plot or histogram


- [ ] What is p-value 🏁 delete
- [ ] What is alpha 🏁 delete 
- [ ] parametric vs non-parametric tests 🏁 delete 
- [ ] tests which don't assume normality 🏁 delete 
- [ ] normality tests' definitions 🏁 delete 