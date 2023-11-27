# Module 3: Peer Reviewed Assignment

### Outline

#### 1. Learn how to read and interpret p-values for coefficients in R:

-  Utilize R's regression analysis packages like `lm()` or `glm()` to fit linear or generalized linear models.
-  Extract model summary using `summary()` to obtain coefficient p-values.
-  Interpret the significance of coefficients based on their p-values.

#### 2. Apply Partial F-tests to compare different models:

-  Create different models using varying predictors or transformations.
-  Use `anova()` to conduct Partial F-tests to compare nested models or test specific hypotheses about nested models.

#### 3. Compute confidence intervals for model coefficients:

-  Extract coefficients from the model using `coef()` or `confint()` to obtain confidence intervals for coefficients.

#### 4. Understand model significance using the Overall F-test:

-  Perform the Overall F-test to evaluate the significance of the entire model.
-  Use `anova()` or model comparison techniques to conduct an F-test for overall significance.

#### 5. Observe the variability of coefficients using the simulated data:

-  Generate simulated data using functions like `rnorm()` or `runif()` to mimic a specific distribution.
-  Fit multiple models to different simulated datasets and observe the variability in coefficient estimates.

Here's an example of how you might approach some of these objectives in an R Jupyter notebook:

```R
# Import necessary libraries
library(ggplot2)

# Simulate data
set.seed(123)
x <- rnorm(100)
y <- 2*x + rnorm(100)

# Fit a linear model
model <- lm(y ~ x)

# View summary of the model
summary(model)

# Extract coefficients and confidence intervals
coefficients <- coef(model)
conf_intervals <- confint(model)

# Perform Partial F-tests or nested model comparisons
# For example:
nested_model <- lm(y ~ 1)  # Model with intercept only
partial_f_test <- anova(nested_model, model)

# Overall F-test for model significance
overall_f_test <- anova(model)

# Simulate data multiple times to observe coefficient variability
simulated_coefficients <- replicate(1000, {
  x_sim <- rnorm(100)
  y_sim <- 2*x_sim + rnorm(100)
  model_sim <- lm(y_sim ~ x_sim)
  coef(model_sim)
})

# Visualize coefficient variability
ggplot(data.frame(simulated_coefficients), aes(x = X.Intercept., y = x)) +
  geom_point(alpha = 0.5) +
  labs(x = "Intercept", y = "Slope") +
  ggtitle("Variability of Coefficients in Simulated Data")
```

Remember, this is a basic example. Depending on your specific dataset and the complexity of your models, you might need to adapt and expand these steps accordingly.

### Problem 1: Individual T-Tests

The dataset below measures the chewiness (mJ) of different berries along with their sugar equivalent and salt (NaCl) concentration. Let's use these data to create a model to finally understand chewiness.

Here are the variables:

1. `nacl`: salt concentration (NaCl)
2. `sugar`: sugar equivalent
3. `chewiness`: chewiness (mJ)

Dataset Source: I. Zouid, R. Siret, F. Jourjion, E. Mehinagic, L. Rolle (2013).
"Impact of Grapes Heterogeneity According to Sugar Level on Both
Physical and Mechanical Berries Properties and their Anthocyanins
Extractability at Harvest," Journal of Texture Studies, Vol. 44, pp. 95-103.

#### 1. (a) Simple linear regression (SLR) parameters

In the below code, we load in the data and fit a SLR model to it, using `chewiness` as the response and `sugar` as the predictor. The summary of the model is printed. Let $\alpha = 0.05$.

Look at the results and answer the following questions:

-  What is the hypothesis test related to the p-value `2.95e-09`? Clearly state the null and alternative hypotheses and the decision made based on the p-value.
-  Does this mean the coefficient is statistically significant?
-  What does it mean for a coefficient to be statistically significant?

```R
# Load the data
chew.data = read.csv("berry_sugar_chewy.csv")

chew.lmod = lm(chewiness ~ sugar, data = chew.data)
summary(chew.lmod)
```

Let's break down the questions based on the provided output of the simple linear regression model:

### Hypothesis Test Related to the p-value `2.95e-09`:

#### Null and Alternative Hypotheses:

-  **Null Hypothesis (H0):** There is no linear relationship between `sugar` and `chewiness` in the population.
-  **Alternative Hypothesis (H1):** There is a linear relationship between `sugar` and `chewiness` in the population.

### Decision based on the p-value:

The p-value associated with the coefficient of `sugar` is `2.95e-09`, which is significantly lower than the significance level of $\alpha = 0.05$. Therefore:

-  We reject the null hypothesis (H0) in favor of the alternative hypothesis (H1).
-  There is sufficient evidence to suggest that there is a linear relationship between `sugar` and `chewiness`.

### Statistical Significance of the Coefficient:

The coefficient for `sugar` is -0.022797. It is accompanied by a small standard error (0.003453) and a very low p-value (`2.95e-09`). The `***` notation in the summary indicates high significance.

-  Yes, the coefficient is statistically significant.
-  When a coefficient is statistically significant, it means that the associated predictor variable (`sugar`, in this case) has a notable impact on the response variable (`chewiness`). In this scenario, the coefficient indicates the average change in `chewiness` per unit change in `sugar`. The small p-value suggests that it is highly unlikely to observe such a strong relationship between `sugar` and `chewiness` if there were no actual linear relationship in the population.

Therefore, based on the p-value being significantly smaller than the chosen significance level (α = 0.05), we have strong evidence to suggest that the `sugar` variable has a statistically significant linear relationship with `chewiness`.

1. _What is the hypothesis test related to the p-value `2.95e-09`? Clearly state the null and alternative hypotheses and the decision made based on the p-value._

   -  The hypothesis test asks whether there is sufficient evidence to reject the null hypothesis in favor of the alternative hypothesis. It accounts for the p-value as it relates to the chosen significance level $\alpha$
   -  The null hypothesis is that there is no effect of sugar on chewiness.
      -  $H_0 : \beta_{sugar} = 0$
   -  The alternative hypothesis is that there is an effect of sugar on chewiness.
      -  $H_1 : \beta_{sugar} \neq 0$
   -  Considering that our p-value of `2.95e-09` is much lower than the significance level of $\alpha = 0.05$, we have sufficient evidence to reject the null hypothesis in favor of the alternative hypothesis.

2. _Does this mean the coefficient is statistically significant?_

   -  Yes, due to our low p-value and consequent rejection of the null hypothesis, the coefficient for `sugar` is statistically significant. The `***` on the row also denotes this significance.

3. _What does it mean for a coefficient to be statistically significant?_

   -  For a coefficient to be statistically significant it means that there is evidence to suggest a linear relationship, or in this case, that sugar has a significant impact on chewiness.

#### 1. (b) MLR parameters

Now let's see if the second predictor/feature `nacl` is worth adding to the model. In the code below, we create a second linear model fitting `chewiness` as the response with `sugar` and `nacl` as predictors.

Look at the results and answer the following questions:

-  Which, if any, of the slope parameters are statistically significant?
-  Did the statistical significance of the parameter for `sugar` stay the same, when compared to 1 (a)? If the statistical signficance changed, explain why it changed. If it didn't change, explain why it didn't change.

```R
chew.lmod.2 = lm(chewiness ~ ., data=chew.data)
summary(chew.lmod.2)
```

Call:
lm(formula = chewiness ~ ., data = chew.data)

Residuals:
Min 1Q Median 3Q Max
-2.3820 -0.6333 0.1234 0.5231 1.9731

Coefficients:
Estimate Std. Error t value Pr(>|t|)
(Intercept) -7.1107 13.6459 -0.521 0.604
nacl 0.6555 0.6045 1.084 0.281
sugar -0.4223 0.3685 -1.146 0.255

Residual standard error: 0.9169 on 87 degrees of freedom
Multiple R-squared: 0.3402, Adjusted R-squared: 0.325
F-statistic: 22.43 on 2 and 87 DF, p-value: 1.395e-08

#### 1. (c) Model Selection

Determine which of the two models we should use. Explain how you arrived at your conclusion and write out the actual equation for your selected model.

```R
anova(chew.lmod,chew.lmod.2)
```

| Res.Df | RSS      | Df  | Sum of Sq | F        | Pr(>F)    |
| ------ | -------- | --- | --------- | -------- | --------- |
| 88     | 74.12640 | NA  | NA        | NA       | NA        |
| 87     | 73.13801 | 1   | 0.9883882 | 1.175719 | 0.2812249 |

#### the ANOVA (ANalysis Of VAriance) function

Interpreting the results of the ANOVA function involves understanding the output table, which compares two nested models. Here's how you can interpret the various columns in the ANOVA table:

#### Columns in the ANOVA Table:

1. **Res.Df (Residual Degrees of Freedom):**

   -  Represents the degrees of freedom associated with the residuals (error terms) of each model.
   -  Lower degrees of freedom indicate fewer data points available for model estimation after considering the number of predictors.

2. **RSS (Residual Sum of Squares):**

   -  Measures the variability that remains unexplained by the model.
   -  Lower RSS values indicate better fitting models, as they capture more variability in the data.

3. **Df (Degrees of Freedom Change):**

   -  Reflects the change in degrees of freedom when comparing the more complex model to the simpler model.
   -  In nested models, it denotes the difference in the number of predictors between the models.

4. **Sum of Sq (Sum of Squares):**

   -  Represents the change in the sum of squared errors between the models.
   -  Indicates the reduction in residual variability when additional predictors are added in the more complex model compared to the simpler one.

5. **F (F-statistic):**

   -  Measures the ratio of explained variance to unexplained variance.
   -  Higher F-values suggest a better fit of the more complex model compared to the simpler model.

6. **Pr(>F) (p-value):**
   -  Assesses the statistical significance of the difference in model fits between the two models.
   -  A low p-value (< chosen significance level, e.g., 0.05) indicates a significant improvement in fit with the more complex model compared to the simpler model.

### Interpreting the Results:

-  **Comparing Model Fits:**

   -  Look at the F-statistic: Higher values suggest a better fit for the more complex model.
   -  Assess the associated p-value: A low p-value indicates a significant difference in model fits.

-  **Degrees of Freedom and Sum of Squares:**

   -  Lower degrees of freedom or higher sum of squares for the residuals suggest a more constrained or less explanatory model.

-  **Decision Making:**
   -  If the p-value is low (< 0.05), it suggests that the more complex model significantly improves the fit.
   -  If the p-value is high (> 0.05), it indicates that there's no significant difference, favoring the simpler model.

### Conclusion:

-  Based on the F-statistic and associated p-value, determine whether the addition of predictors in the more complex model significantly enhances the model's fit compared to the simpler model.
-  A low p-value suggests a preference for the more complex model, whereas a high p-value favors the simpler model.

The `chew.lmod` model represents the simple linear regression model with `chewiness` as the response variable and `sugar` as the predictor variable. In a mathematical formula, the equation for this linear regression model is written as:

\[
\text{chewiness} = \beta*0 + \beta*{\text{sugar}} \times \text{sugar} + \epsilon
\]

Where:

-  \(\text{chewiness}\) represents the dependent variable (response variable) - the chewiness measurement.
-  \(\text{sugar}\) is the independent variable (predictor variable) - the sugar equivalent.
-  \(\beta_0\) is the intercept term, representing the value of \(\text{chewiness}\) when the predictor (\(\text{sugar}\)) is zero.
-  \(\beta\_{\text{sugar}}\) is the coefficient for the predictor variable \(\text{sugar}\), indicating the change in the dependent variable (\(\text{chewiness}\)) for a unit change in the predictor.
-  \(\epsilon\) represents the error term, which accounts for the variability in the dependent variable that is not explained by the predictor variable.

The estimated coefficient values for \(\beta*0\) and \(\beta*{\text{sugar}}\) are derived from the regression analysis and can be obtained from the summary output of the `chew.lmod` model in R. Substituting these estimated coefficients into the equation provides the specific mathematical representation of the linear regression model for `chew.lmod`.

-  The results of calling anova(chew.lmod, chew.lmod.2) allow us to confidently state that we should use chew.lmod as our model, as the introduction of the 2nd variable leads to less statistically significant results. I came to this conclusion through a few observations:

   -  First, as denoted by Pr(>F), the p-value is 0.2812249. This is much higher than our significance level of $\alpha = 0.05$, and as a result, we cannot reject the Null Hypothesis. In this case the null hypothesis states that there is no significant difference between the two models.
   -  If there is no significant difference in the models, we should opt for the less complex model. The `Df` value tells us that there is one more predictor in chew.lmod.2 than chew.lmod, and as such, it is the more complex and less desirable model in this situation.
   -  The equation for model 1 could be written as:
      -  $\text{chewiness} = \beta_0 + \beta_{\text{sugar}} \times \text{sugar} + \epsilon$
   -  where:
      -  `chewiness` is the response
      -  `sugar` is the predictor and represents the sugar equivalent
      -  $\beta_0$ is the intercept and respresents `chewiness` when `sugar` is zero
      -  $\beta_{\text{sugar}}$ indicates the change in chewiness per unit change in sugar
   -  substituting the values from our summary of the model we get:
      -  $\text{chewiness} = 7.662878 - 0.022797 \times \text{sugar} + \epsilon$

#### 1. (d) Parameter Confidence Intervals

Compute $95\%$ confidence intervals for each parameter in your selected model. Then, in words, state what these confidence intervals mean.

```R
# Your Code Here
conf_ints = confint(chew.lmod)
conf_ints
```

which returns

A matrix: 2 × 2 of type dbl
| | 2.5 % | 97.5 % |
|-----------|------------|------------|
|(Intercept)| 6.15927388 | 9.16648152 |
| sugar | -0.02965862| -0.01593536|

These confidence intervals allow us to estimate a range of possible values for the regression parameters. For example, the output above suggests that the intercept, or the chewiness measurement when sugar is zero, ranges from 6.15927388 to 9.16648152. The sugar coefficient, meanwhile, ranges from -0.02965862 to -0.01593536. This means that we can say with 95% confidence that the true value for the intercept and and coefficient lies within those respective ranges.

In linear regression analysis, confidence intervals are used to estimate the range of plausible values for the regression coefficients (parameters) associated with predictor variables. These intervals provide insights into the uncertainty and precision of the estimated coefficients in the regression model.

### Use of Confidence Intervals in Linear Regression Parameters:

1. **Estimating Coefficients:** For each predictor variable in a linear regression model, there's an estimated coefficient (slope) that quantifies the relationship between that predictor and the response variable.

2. **Understanding Uncertainty:** Confidence intervals around these coefficients provide a range of values within which the true population coefficient is expected to lie with a certain level of confidence (e.g., \(95\%\) confidence intervals).

3. **Assessing Significance:** If a confidence interval does not contain zero, it suggests that the coefficient is statistically significant at the chosen significance level (often \(0.05\)). A confidence interval excluding zero indicates that the predictor variable has a non-zero effect on the response variable.

4. **Interpreting Effect Sizes:** Wider intervals imply greater uncertainty in estimating the true coefficient value, while narrower intervals indicate higher precision in estimating the parameter.

5. **Comparing Variables:** Comparing the confidence intervals of coefficients between different predictor variables helps identify which predictors have stronger or more reliable effects on the response variable.

6. **Model Building and Selection:** Confidence intervals aid in assessing the importance of predictors. For instance, if the interval for a coefficient spans a wide range including zero, it might suggest considering dropping that predictor from the model.

7. **Communicating Results:** They provide valuable information for communicating the precision and uncertainty of the estimated coefficients to stakeholders, researchers, or decision-makers.

In summary, confidence intervals for regression coefficients in linear regression help in understanding the range of plausible values for these coefficients, assessing their significance, comparing the importance of predictors, and making informed decisions about the model's variables and structure.

# Problem 2: Variability of Slope in SLR

In this exercise we'll look at the variability of slopes of simple linear regression models fitted to realizations of simulated data.

Write a function, called `sim_data()`, that returns a simulated sample of size $n = 20$ from the model $Y = 1 + 2.5X + \epsilon$ where $\epsilon \overset{iid}{\sim} N(0, 1)$. We will then use this generative funciton to understand how fitted slopes can vary, even for the same underlying population.

```R
sim_data <- function(n=20, var=1, beta.0=1, beta.1=2.5){
    # BEGIN SOLUTION HERE
    x = seq(-1, 1, length.out = n); beta0 = 1; beta1 = 2.5; e = rnorm(n, 0, sqrt(var))
    y = beta0 + beta1*x + e
    # END SOLUTION HERE
    data = data.frame(x=x, y=y)
    return(data)
}
```

#### 2. (a) Fit a slope

Execute the following code to generate 20 data points, fit a simple linear regression model and plot the results.

Just based on this plot, how well does our linear model fit the data?

```R
data = sim_data()
lmod = lm(y~x, data=data)
ggplot(aes(x=x, y=y), data=data) +
    geom_point() +
    geom_smooth(method="lm", formula=y~x, se=FALSE, color="#CFB87C")
```

#### 2. (b) Do the slopes change?

Now we want to see how the slope of our line varies with different random samples of data. Call our data genaration funciton $50$ times to gather $50$ independent samples. Then we can fit a SLR model to each of those samples and plot the resulting slope. The function below performs this for us.

Experiment with different variances and report on what effect that has to the spread of the slopes.

As the variance number increases the distribution of the slopes also increases. An increase by a factor of ten led to a very loose distribution of slopes some of which even differed in direction.

#### 2. (c) Distributions of Slopes

As we see above, the slopes are somewhat random. That means that they follow some sort of distribution, which we can try to discern. The code below computes `num_samples` independent realizations of the model data, computes the SLR model, and generates a histogram of the resulting slopes.

Again, experiment with different variances for the simulated data and record what you notice. What do you notice about the shapes of the resulting histograms?

```R
hist_slopes <- function(num.slopes=500, var=1, num.samples=20){
    slopes = rep(0, num.slopes)
    # For num.slopes, compute a SLR model slope
    for(i in 1:num.slopes){
        # Simulate the desired data
        data = sim_data(var=var, n=num.samples)
        # Fit an SLR model to the data
        lmod = lm(y~x, data=data)
        # Add the slopes to the vector of slopes
        slopes[i] = lmod$coef[2]
    }
    # Plot a histogram of the resulting slopes
    g = ggplot() + aes(slopes) + geom_histogram(color="black", fill="#CFB87C")
    return(g)
}

hist_slopes(0.5);
hist_slopes(2);
hist_slopes(5);
```

As variance increases the histograms tend to display broader shapes and wider distributions while lower variance has slopes more concentrated around a specific value.

#### 2. (d) Confidence Intervals of Slopes

What does that all mean? It means that when we fit a linear regression model, our parameter _estimates_ will not be equal to the true parameters. Instead, the estimates will vary from sample to sample, and form a distribution. This is true for any linear regression model with any data - not just simulated data - as long as we assume that there is a large population that we can resample the response from (at fixed predictor values). Also note that we only demonstrated this fact with the slope estimate, but the same principle is true for the intercept, or if we had several slope parameters.

This simulation shows that there is a chance for a linear regression model to have a slope that is very different from the true slope. But with a large sample size, $n$, or small error variance, $\sigma^2$, the distribution will become narrower. Confidence intervals can help us understand this variability. The procedure that generates confidence intervals for our model parameters has a high probability of covering the true parameter. And, the higher $n$ is, for a fixed $\sigma^2$, or the smaller $\sigma^2$ is, for a fixed $n$, the narrower the confidence interval will be!

Draw a single sample of size $n=20$ from `sim_data()` with variance $\sigma^2 = 1$. Use your sample to compute a 95% confidence interval for the slope. Does the known slope for the model (which we can recall is $2.5$) fall inside your confidence interval? How does the value of $\sigma^2$ affect the CI width?

```R
# Your code here
single_sample = sim_data(n = 20, var = 1)
lm_single_sample = lm(y ~ x, data = single_sample)

summary(lm_single_sample)

conf_int = confint(lm_single_sample)
conf_int

```
