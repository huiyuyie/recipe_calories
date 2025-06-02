# An Analysis of Recipe Preparation Time and Calorie Content
_A Data Science Project using food.com Recipes_

---

## INTRODUCTION

This project investigates the **Recipes and Ratings** dataset from [food.com](https://www.food.com/), consisting of thousands of recipes and user ratings since 2008. After merging and cleaning the raw data, our final working dataset constains **234429 rows**.

### Research Question

**Whether the time taken to make a recipe affect the number of calories?**

---

This question is important because understanding the relationship between cooking time and number of calories can help home cooks make better dietary decisions and optimize their meal plannings. For instance, people may asusme that recipes which takes longer to make are more indulgent, while quick recipes might be healthier. By analyzing this relationship, we can provide insights for users aiming for specific nutritional or time goals.

**Why this question matters?**
 Millions of people choose recipes based on preparation time and perceived healthiness. If there is a significant association between cooking time and the number of calories, users can make more informed choices according to their time constraints and nutritional goals. This analysis can also supplort food bloggers, diaticians, and recipe platforms in designing and recommeding recipes that meet different users' needs.


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
| name                                 |   minutes | time_range   |   calories |   avg_rating |   n_tags |
|:-------------------------------------|----------:|:-------------|-----------:|-------------:|---------:|
| 1 brownies in the world    best ever |        40 | 30-60        |      138.4 |            4 |      219 |
| 1 in canada chocolate chip cookies   |        45 | 30-60        |      595.1 |            5 |      157 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |            5 |      148 |
| millionaire pound cake               |       120 | 120-240      |      878.3 |            5 |      290 |
| 2000 meatloaf                        |        90 | 60-120       |      267   |            5 |      150 |
| 2000 meatloaf                        |        90 | 60-120       |      267   |            5 |      150 |
| 5 tacos                              |        20 | <30          |      249.4 |            4 |      352 |

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

This table shows that recipes with longer preparation times tend to have higher average calorie counts, but user ratings remain relatively consistent across all time ranges. Interestingly, recipes that can be prepared in less than 30 minutes tend to have the most tags, possibly reflecting their popularity.


## Assessment of Missingness
After inspecting missing values across all columns, we identified that the `description`, `rating`, and `review` columns have notable amounts of missing data. There are 114 missing descriptions, 15,036 missing ratings, and 58 missing reviews. There are also 2,777 missing average ratings, but we are not analyzing this column since its missingness is directly related to the missingness of ratings.

### NMAR Analysis
It is possible that the missingness in the `review` column is **NMAR (Not Missing At Random)**. Users may be less likely to leave a review for recipes they did not enjoy, or may only leave a review if they had an unusually positive or negative experience. In this case, the probability of a missing value in the `review` column would depend on the unobserved review itself.

Similarly, the missingness in the `rating` column could also be **NMAR**. Users may be more likely to omit ratings for recipes they did not like or for which they had no strong opinion. Thus, the missingness could depend on the unobserved (missing) rating.

To confirm whether the missingness is truly NMAR, we would need more information about why users chose not to leave a rating or review. For example, if we had access to user engagement data or survey responses, we might be able to distinguish between NMAR and MAR.

**In summary**, there is evidence that at least the `review` and possibly the `rating` columns exhibit **NMAR** missingness, as the likelihood of missingness may depend on the values that are themselves missing.

### Missingness Dependency
We choose to analyze the missingness of the `rating` columns since it has the most number of missing values. 
To investigate whether the missingness of the `rating` column depends on other variables, we performed permutation tests using the following two test statistics:
  - absolute mean difference for numerical variables, e.g. `minutes`, `n_tags` , etc.
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
We used a permutation test here since it does not assume that calories are normally distributed, which makes sence for recipe data that can have a lot of variety and outliers. 


## Framing a Prediction Problem

We formulate a **regression problem** to predict the total number of calories in a recipe, given information that would be known before it is prepared.

The response variable is `calories`, this is the total number of calories of a recipe. 
**Why this variable?** Since calories are a core nutritional attribute that users often care about when selecting or comparing recipes. Accurately predicting calories can help users make informed, health-conscious choices or meet dietary goals.

The **metric** we are using is R^2, it is a standard metric for regression tasks. Since it measures the proportion of variance in the target explained by the model, we can interpret the model performance by looking at how close R^2 is to 1. 

**Why R^2 over MAE?** 
R^2 gives us a sense of how much of the variation in number of calories our model can explain, rather than just how far off our predictions are on average. While MAE tells us the average prediction error, it doesn't tell us how well the model captures the overall patterns in the data.

Justification: At the time of prediction, we only use features that would be known BEFORE the recipe is actually prepared, for example the time range and the number of steps. We don't use any features that depend on the outcome, since thos would not be availablt when users are considering or planning to make the recipe. This ensures our predictions are realistic.


## Baseline Model

## Final Model

## Fairness Analysis




