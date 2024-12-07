# Recipe Rating Analysis

# Introduction

The datasets used in this project contain recipes and ratings from [food.com](https://www.food.com). They contain the recipes and reviews posted since 2008. 

> What types of recipes tend to have higher ratings?

Insights about patterns that make a recipe highly rated can help anyone improve their cooking. For example, recipe creators can design recipes that are more likely to be enjoyed by their community, while recipe searchers can more easily find recipes that they love.

The cleaned dataset has . The columns that are relevant to this question are `minutes` (the preparation time in minutes), `submitted` (the year and month that the recipe was submitted), `nutrition` (nutritional information), `n_steps` (the number of steps), and `n_ingredients` (the number of ingredients). 

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning

**Splitting nutrition data into columns:** The `nutrition` information was in the form of a single string - I split this string into individual columns representing key nutrients like `calories`, `total_fat`, `sugar`, etc. This allowed each nutrient to be treated as a separate variable, leading to more meaningful exploratory data analysis.

**Handling timestamps:** The `submitted` column contains recipe submission dates, and I split it into two columns, `year_submitted` and `month_submitted`. This allowed me to identify any potential seasonal trends, as well as the distribution of ratings over time.

**Capping extreme values:** Through plotting the data, I found that there were extreme outliers in features like `n_steps`, `minutes`, and `calories`, so I capped the values at reasonable limits based on my general knowledge of cooking. For example:
- Recipes with a preparation time of over 200 minutes were removed
- Recipes with over 50 steps were removed
- Recipes with over 1,000 calories were removed
This reduced the influence of outliers, which may have skewed predictions.

**Filtering nutrition percent daily values (PDVs):** Nutrition features like `total_fat`, `sugar`, and `protein` represent percentages of daily values. I capped these at 100%, keeping only realistic PDVs. This made the nutritonal data more valuable for investigation by making it more realistic.

**Handling missing values:** I replaced blank or `None` values with `np.nan` to standardize missing data across the dataset. Rows missing the target variable (`rating`) were removed at the time of modeling. This allowed for consistency in missing data during exploratory data analysis and predictive modeling. 

## Univariate Analysis

<iframe
  src="assets/fig_prep_time.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The histogram above shows the distribution of preparation times for recipes in the dataset. Most recipes require 20-50 minutes, with a sharp decline in recipes taking longer than 50 minutes. This shows that recipes with shorter preparation times are more common, potentially suggesting user prefrence for quicker dishes. 

This insight ties back to our initial question: *What types of recipes tend to have higher ratings?* By understanding that shorter preparation times are more common, we can hypothesize that user ratings might favor recipes that are quicker and easier to prepare. 

## Bivariate Analysis

<iframe
  src="assets/fig_ratings_per_year.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I had hypothesized that older recipes on the website would have a greater number of ratings, and therefore a potentially higher average rating. The bar chart above shows the number of ratings submitted per year. There is a noticeable downward trend over time, with recipes from 2008 having the greatest number of ratings.

This suggests that recipes that have been on the website for longer likely have more engagement. This is relevant to the initial question because it highlights the need to consider time-related factors when analyzing recipe ratings.

# Framing a Prediction Problem

# Baseline Model

# Final Model