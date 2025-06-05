# An Analysis of Recipe Preparation Time and Calorie Content
_A Data Science Project using food.com Recipes_
__

---

## INTRODUCTION

This project investigates the **Recipes and Ratings** dataset from [food.com](https://www.food.com/), consisting of thousands of recipes and user ratings since 2008. After merging and cleaning the raw data, our final working dataset contains **234429 rows**.

### Research Question

**Whether the time taken to make a recipe affect the number of calories?**

---

This question is important because understanding the relationship between cooking time and number of calories can help home cooks make better dietary decisions and optimize their meal plannings. For instance, people may assume that recipes which takes longer to make are more indulgent, while quick recipes might be healthier. By analyzing this relationship, we can provide insights for users aiming for specific nutritional or time goals.

**Why this question matters?**
 Millions of people choose recipes based on preparation time and perceived healthiness. If there is a significant association between cooking time and the number of calories, users can make more informed choices according to their time constraints and nutritional goals. This analysis can also support food bloggers, dietitians, and recipe platforms in designing and recommeding recipes that meet different users' needs.


## Data Cleaning and Exploratory Data Analysis
To ensure accurate and meaningful analysis, we performed several key data cleaning steps:

1. **Merging Datasets**
We combined the recipes and interactions datasets using left join on the recipe ID. This ensures every recipe, even those without user rating, is included in our analysis.

2.  **Handling invalid Ratings**
We replaced all ratings of 0 with `NaN`. According to the data documentation, a rating of 0 likely means a missing or invalid value, not a true rating. Treating these as missing prevents them from distorting our calculations of average ratings.

3. **Calculating Average Ratings**
For each recipe, we computed the average rating across all users and stored it in a new column, `avg_rating`. This allows us to fairly compare recipes even if they have different numbers of ratings.

4. **Parsing Nutrition Information**
The `nutrition` column in the raw data is a string representation of a list of seven nutritional values:  
   `[calories, total fat (% daily value), sugar (% daily value), sodium (% daily value), protein (% daily value), saturated fat (% daily value), carbohydrates (% daily value)]`  
We wrote helper functions to extract each value and created new numeric columns for each nutritional component. This enables direct analysis of calories, fat, sugar, and more.


5. **Binning Preparation Time**
We created a new column, `time_range`, by binning the `minutes` column into categorical time intervals (e.g., `<30`, `30-60`, `60-120`, etc.). This helps us analyze patterns across different preparation durations and reduces the effect of outliers.

6. **Processing Tags**
We converted the `tags` column from a string to a list, and counted the number of tags for each recipe (`n_tags`). This allows us to analyze how tag richness may relate to other recipe features.

---

## Relevant Columns and Descriptions

| Column Name   | Description                                                               |
|:------------- |:------------------------------------------------------------------------- |
| `name`        | Name/title of the recipe.                                                 |
| `minutes`     | Preparation time (in minutes) required for the recipe.                    |
| `time_range`  | Categorical bin of `minutes` (e.g., <30, 30-60, 60-120, etc.)             |
| `calories`    | Total number of calories in the recipe.                                   |
| `avg_rating`  | Average user rating for the recipe (on a 0–5 scale).                      |
| `n_tags`      | Number of tags assigned to the recipe, reflecting its features or themes. |


### Head of the Cleaned DataFrame

| name                                 | minutes | time_range | calories | avg_rating | n_tags |
|:-------------------------------------|--------:|:-----------|---------:|-----------:|-------:|
| 1 brownies in the world best ever    |      40 | 30-60      |    138.4 |          4 |    219 |
| 1 in canada chocolate chip cookies   |      45 | 30-60      |    595.1 |          5 |    157 |
| 412 broccoli casserole               |      40 | 30-60      |    194.8 |          5 |    148 |
| 412 broccoli casserole               |      40 | 30-60      |    194.8 |          5 |    148 |
| 412 broccoli casserole               |      40 | 30-60      |    194.8 |          5 |    148 |
| 412 broccoli casserole               |      40 | 30-60      |    194.8 |          5 |    148 |
| millionaire pound cake               |     120 | 120-240    |    878.3 |          5 |    290 |
| 2000 meatloaf                        |      90 | 60-120     |      267 |          5 |    150 |
| 2000 meatloaf                        |      90 | 60-120     |      267 |          5 |    150 |
| 5 tacos                              |      20 | <30        |    249.4 |          4 |    352 |

## Univariate Analysis
To understand the characteristics of our dataset, we visualized the distributions of key variables. Below, we display and interpret two important univariate plots.

--- 

### Distribution of Recipes by Preparation Time
<iframe
  src="assets/timerange-vs-recipecount.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*Figure: Number of recipes in each preparation time range.*

Most recipes on food.com can be prepared in under 60 minutes, as shown by the concentration in the first two time bins. As preparation time increases, the number of available recipes drops sharply. This indicates that quick and convenient recipes are more common and possibly more popular among users.

---

### Distribution of Recipe Calories
<iframe
  src="assets/recipeindex-vs-calories.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*Figure: Scatter plot of recipe calorie counts by recipe index.*

The scatter plot above shows the spread of calories across all recipes in our dataset. Most recipes cluster in the lower-to-moderate calorie range, but we can observe a few recipes with extremely high calorie counts. These outliers may represent more indulgent or special-occasion dishes, while the dense cluster in the lower range reflects the prevalence of healthier or more everyday recipes.

## Bivariate Analysis
To investigate the relationship between recipe preparation time and calorie content, we created several bivariate visualizations.

---

### Average Calories by Preparation Time Range
<iframe
  src="assets/timerange-vs-avgcalories.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*Figure: Average calories of recipes for each preparation time range.*

The bar chart above shows that recipes with longer preparation times tend to have higher average calories. This trend suggests that more time-consuming recipes may involve richer or more calorie-dense ingredients.

---

### Calories vs. Preparation Time (Scatter Plot)
<iframe
  src="assets/preptime-vs-calories.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*Figure: Scatter plot of calories versus preparation time for individual recipes, with regression trendline.*

The scatter plot displays a positive but weak correlation between preparation time and calories, as indicated by the upward trendline. While there are many recipes with both low and high calorie counts across all preparation times, some of the longest recipes do appear to be more calorie-dense.

---

## Interesting Aggregates
To further explore patterns in our dataset, we computed aggregate statistics for several key variables, grouped by preparation time range:

| time_range   |   calories |   avg_rating |   n_tags |
|:-------------|-----------:|-------------:|---------:|
| <30          |     327.3  |         4.7  |   256.34 |
| 30-60        |     411.03 |         4.67 |   237.42 |
| 60-120       |     540.33 |         4.67 |   236.17 |
| 120-240      |     626.74 |         4.66 |   238.53 |
| 240-480      |     519.16 |         4.57 |   228.49 |
| >480         |     515.79 |         4.63 |   228.14 |

*Table: Mean calories, average user rating, and mean number of tags for recipes in each preparation time range.*

This group-by table is valuable because it reveals how key recipe attributes vary with preparation time, allowing us to compare trends across different categories. This table shows that recipes with longer preparation times tend to have higher average calorie counts, but user ratings remain relatively consistent across all time ranges. Interestingly, recipes that can be prepared in less than 30 minutes tend to have the most tags, possibly reflecting their popularity. Summarizing the data in this way helps us uncover meaningful patterbs that would be difficult to observe by looking at individual recipes alone.


## Assessment of Missingness
After inspecting missing values across all columns, we identified that the `description`, `rating`, and `review` columns have notable amounts of missing data. There are 114 missing descriptions, 15,036 missing ratings, and 58 missing reviews. There are also 2,777 missing average ratings, but we are not analyzing this column since its missingness is directly related to the missingness of ratings.

### NMAR Analysis
It is possible that the missingness in the `review` column is **NMAR (Not Missing At Random)**. Users may be less likely to leave a review for recipes they did not enjoy, or may only leave a review if they had an unusually positive or negative experience. In both cases, the probability of a missing value in the `review` column would depend on the unobserved review itself.

To confirm whether the missingness is truly NMAR, we would need more information about why users chose not to leave a rating or review. Additional data, like user engagement logs, explicit feedback about why a review was left blank, or follow-up surveys, could help clarify whether the missingness is actually related to the underlying, unobserved review value, or if it could be explained by observed factors which would make it Missing At Random. 

**In summary**, there is evidence that at least the `review` column exhibit **NMAR** missingness, as the likelihood of missingness may depend on the values that are themselves missing.

### Missingness Dependency
We choose to analyze the missingness of the `rating` columns since it has the most number of missing values. 
To investigate whether the missingness of the `rating` column depends on other variables, we performed permutation tests using the following two test statistics:
  - absolute mean difference for numerical variables, e.g. `minutes`, `n_tags`, etc.
  - total variation distance for categorical variables. e.g. `time_range`, etc.

We tested the following pairs:
- `rating` missingness vs. `minutes` (raw preparation time): **p-value = 0.126** (not dependent)
- `rating` missingness vs. `n_tags` (number of tags): **p-value = 0.0** (dependent)
- `rating` missingness vs. `time_range` (preparation time bin): **p-value = 0.0** (dependent)

Intepretation:
There is strong evidence that the missingness of `rating` is associated with the number of tags, and preparation time range. However, it is not significantly associated with the raw preparation time in minutes.

<iframe
  src="assets/rating-missing-vs-ntags.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
*Figure: Distribution of number of tags for recipes with and without missing ratings.*
There is a clear difference in the distribution of `n_tags` between recipes with missing ratings and those with available ratings. This visual evidence, together with our permutation test, suggests that rating missingness depends on the number of tags.

---

## Hypothesis Testing
To assess whether recipes that take between **120 and 240 minutes** to prepare have different mean calorie counts compared to all other recipes, we performed a permutation test as follows:

**Null Hypothesis (H0):**
There is **NO difference** in the mean calories of recipes that take 120~240 minutes to make.

**Alternative Hypothesis (H1):**
There **is a difference** in the mean calories of recipes that take 120~240 minutes to make and those that do not.

**Test Statistic:**
The absolute difference in mean calories between the two groups.

**Significance level:**
We used a significance level (**alpha**) of 0.05

**Results:**
The observed absolute difference in mean calories was 218.4852.
Our permutation test produced a p-value of 0.0

**Conclusion:**
Since the p-value 0.0 is below our significant level of 0.05, we REJECT the null hypothesis. This provides strong evidence that the mean calories of recipes that take 120~240 minutes to prepare is different from that of other recipes in our dataset. 

**Justification**
We choose to do a permutation test since it doesn't require any distributional assumptions about calories.This is especially important given that calorie values can be highly skewed and contain outliers. he method is robust, intuitive, and well-suited for comparing means between two groups when traditional parametric assumptions may not hold. We used the  **absolute mean difference** as our test statistic since we are performing a two tailed test. This approach is well-suited for comparing two groups and provides an intuitive way to access whether the observed difference in calories could be due to random chance. 

---

## Framing a Prediction Problem
We formulate a **regression problem** to predict the total number of calories in a recipe, given information that would be known before it is prepared.

The response variable is `calories`, this is the total number of calories of a recipe. 
**Why this variable?** Since calories are a core nutritional attribute that users often care about when selecting or comparing recipes. Accurately predicting calories can help users make informed, health-conscious choices or meet dietary goals.

The **metric** we are using is R^2, it is a standard metric for regression tasks. Since it measures the proportion of variance in the target explained by the model, we can interpret the model performance by looking at how close R^2 is to 1. 

**Why R^2 over MAE?** 
R^2 gives us a sense of how much of the variation in number of calories our model can explain, rather than just how far off our predictions are on average. While MAE tells us the average prediction error, it doesn't tell us how well the model captures the overall patterns in the data.

Justification: At the time of prediction, we only use features that would be known BEFORE the recipe is actually prepared, for example the time range and the number of steps. We don't use any features that depend on the outcome, since those would not be available when users are considering or planning to make the recipe. This ensures our predictions are realistic.

--- 

## Baseline Model
For our baseline regression model predicting the total number of calories in a recipe, we included two features:
`time_range` (ordinal / categorical): the preparation time range, group into ordered bins (<30, 30-60, 60-120, 120-240, 240-480, >480 minutes)
Since this is a ordinal feature, we used an OrdinalEncoder to encode this feature, ensuring that the ordering reflects increasing preparation times.
`n_steps` (quantitative / numerical): the number of preparation steps for each recipe.
Since this is a quantitative feature, it's already numerical, so we left it as it is.

We built a pipeline that first applies the appropriate preprocessing to each feature (passing through the numeric one and ordinally encoding the time range), then fits a Ridge regression model. We chose Ridge regression over simple linear regression since it can handle multicollinearity and prevent overfitting. Recipe data often includes highly correlated features, such as ingredient proportions and preparation steps, which can cause instability in linear models. Ridge adds L2 regularization performance, making it more suitable for prediction tasks where robustness matters more than interpretability. 

**Model performance** I
Test set R^2: 0.026
Train set R^2: 0.023
The R^2 value represents the proportion of the variance in calorie counts explained by our model. In this baseline, the values are quite low, indicating that these two features alon DO NOT capture much of the variability in recipe calorie counts. This is not surprising, as calorie content is likely influenced by many other factors not included in the baseline model.

**Is this model "good"?**
According to the above analysis, this model does not predict calories well and serves to establish a baseline for future models. By adding additional features, or improving feature engineering, we hope to significanly improve the predictive power. 

--- 

## Final Model

For our final model, we included two new features:

- **Protein-to-Fat Ratio (`prop_protein_to_fat`)**:
  This feature is the ratio of protein to total fat in each recipe. We chose it since, nutritionally, the balance between protein and fat significantly affects the caloric denity and healthiness of a recipe. Recipes with higher protein-to-fat ratio are often considered healthier and may have lower total calories. By including this feature, our model can better capture the nutritional quality and caloric density that simpler features may miss.

- **Proportion of Sugar (`prop_sugar`)**
  We applied a binarizer to mark whether the number of sugar of a recipe exceeds a threshold. We chose this to be an additional feature since it is one of the main contributor to calories. Including a feature to highlight high-sugar recipes allows the model to account for non-linear impact of sugar on total calories, thus improving prediction accuracy for both typical and exceptionally sweet recipes.

Both features were chosen *before* observing model performance, based on a principled understanding of how ingredients affect calories and why certain variables might capture important variation in the outcome.

**Modeling Algorithm**
We performed Ridge Regression for our final model, which is a regularized linear regression algorithm that penalizes larger coefficients, helping to reduce overfitting.

**Hyperparameter used**
- `alpha`: This parameter controls the regularization strength. We searched over [0.01, 0.1, 1.0, 10.0, 100.0], which is a log-scale grid that covers a wide range of possible regularization values. Using a range that spans multiple magnitudes helps ensure that we find an appropriate balance between bias and variance. If `alpha` is too low, the model may overfit the training data; if `alpha` is too high, the model may underfit and ignore important signals.

- We used GridSearchCV to search for the bset value of `alpha` based on 5-fold cross validation. This method splits the training data into five parts, trains the model on four and validates on the fifth, repeating this process and average the results. 

- The metric we used here is `neg_mean_squared_error`, since GridSearchCV always tries to maximize the scoring metric, and the lower MSE is, the better predictions were made. To make this compatible, using the negative of MSE that will be maximized here is equivalent to minimizing MSE. By using the negative of MSE, GridSearchCV effectively finds the model with the lowest MSE, which means the best regression performance.

**Results of the Final Model**
R^2 (test set): 0.043
R^2 (train set): 0.039

Our final model showed an improvement in R^2, explaining more variance in the number of calories in recipes. This shows that adding features makes the model better at predicting calories. However, since R^2 is still very far away from 1, the model is not doing a good job at explaining the variance though there is improvement.

---

## Fairness Analysis
To evaluate whether our final model performs equitably across different groups, we performed a fairness analysis by comparing model performance for two groups based on preparation time:
- **Group X**: Recipes with preparation time ≤ 120 minutes
- **Group Y**: Recipes with preparation time > 120 minutes

We used the coefficient of determination, R^2, as our evaluation metric since we are working with a regression problem. 
- **Null Hypothesis (H0):** The model's R^2 for recipes in both groups is the same; any observed difference is due to chance. (the model is fair with respect to preparation time)
- **Alternative Hypothesis (H1):** The model's R^2 is different between the two groups, the model may perform better for one group than the other. (the model is not fair)

**Test Statistic**: the test statistic we use is the absolute difference in R^2 scores between Group X and Group Y, since we are doing a two side test

**Significance Level**: a significance level of 0.05

We performed a permutation test by randomly shuffling the group assignments 1000 times and recalculating the R^2 difference for each permutaion. The observed p-val was 0.002

**Conclusion**:
The resulting **p-value was 0.002**, which is much smaller than our significance threshold of 0.05. This provides strong evidence that the model's predictive performance is *not* the same for both groups—our model may be less fair with respect to preparation time. This could mean that it predicts calories more accurately for recipes with shorter or longer preparation times, but not both.


## Wrapping Up
In this project, we set out to understand how recipe preparation time relates to calorie content. We cleaned and explored a large dataset, finding that recipes taking longer to make usually pack more calories, while user ratings are pretty steady no matter the time required.

We built a baseline calorie prediction model and then improved it with features inspired by nutrition—like the protein-to-fat ratio and a high/low sugar flag. While these additions helped a bit, predicting calories from recipe info alone turned out to be quite tough, as shown by our modest R^2 scores.

Our fairness check also showed the model doesn’t perform equally well for all types of recipes—so there’s still work to do if we want to make predictions that are both accurate and fair.

All in all, this project highlighted the value of smart feature engineering, careful testing, and thinking about fairness. There’s room to improve, but we’ve taken some solid steps toward making recipe recommendations that are both useful and mindful of users’ needs.