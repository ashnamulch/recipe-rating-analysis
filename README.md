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

**Filtering nutrition percent daily values (PDVs):** Nutrition features like `total_fat`, `sugar`, and `protein` represent percentages of daily values. I capped these at 100%, keeping only realistic PDVs.

# Framing a Prediction Problem

# Baseline Model

# Final Model