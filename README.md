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

### Head of the Cleaned DataFrame
| name                                 |   minutes | time_range   |   calories |   prop_total_fat |   prop_sugar |   prop_protein |   avg_rating |   n_tags |
|:-------------------------------------|----------:|:-------------|-----------:|-----------------:|-------------:|---------------:|-------------:|---------:|
| 1 brownies in the world    best ever |        40 | 30-60        |      138.4 |               10 |           50 |              3 |            4 |      219 |
| 1 in canada chocolate chip cookies   |        45 | 30-60        |      595.1 |               46 |          211 |             13 |            5 |      157 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |               20 |            6 |             22 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |               20 |            6 |             22 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |               20 |            6 |             22 |            5 |      148 |
| 412 broccoli casserole               |        40 | 30-60        |      194.8 |               20 |            6 |             22 |            5 |      148 |
| millionaire pound cake               |       120 | 120-240      |      878.3 |               63 |          326 |             20 |            5 |      290 |
| 2000 meatloaf                        |        90 | 60-120       |      267   |               30 |           12 |             29 |            5 |      150 |
| 2000 meatloaf                        |        90 | 60-120       |      267   |               30 |           12 |             29 |            5 |      150 |
| 5 tacos                              |        20 | <30          |      249.4 |               26 |            4 |             39 |            4 |      352 |

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


## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis




