# Recipe Rating Analysis

# Introduction

The datasets used in this project contain recipes and ratings from [food.com](https://www.food.com). They contain the recipes and reviews posted since 2008. 

> What types of recipes tend to have higher ratings?

Insights about patterns that make a recipe highly rated can help anyone improve their cooking. For example, recipe creators can design recipes that are more likely to be enjoyed by their community, while recipe searchers can more easily find recipes that they love.

The raw dataset has 83,782 rows, and the cleaned dataset used for modeling has 81,173 rows. The columns that are relevant to this question are `minutes` (the preparation time in minutes), `submitted` (the year and month that the recipe was submitted), `nutrition` (nutritional information), `n_steps` (the number of steps), and `n_ingredients` (the number of ingredients). 

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

**First 5 rows of the relevant columns of the cleaned table:**

| name                                    |   minutes |   year_submitted |   month_submitted |   n_steps |   n_ingredients |   rating |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|:----------------------------------------|----------:|-----------------:|------------------:|----------:|----------------:|---------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 1 brownies in the world    best ever    |        40 |             2008 |                10 |        10 |               9 |        4 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
| 412 broccoli casserole                  |        40 |             2008 |                 5 |         6 |               9 |        5 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
| 2000 meatloaf                           |        90 |             2012 |                 3 |        17 |              13 |        5 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |
| 5 tacos                                 |        20 |             2013 |                 5 |         5 |               9 |        4 |      249.4 |          26 |       4 |        6 |        39 |              39 |               0 |
| blepandekager   danish   apple pancakes |        50 |             2013 |                 7 |        10 |              10 |        5 |      358.2 |          30 |      62 |       14 |        19 |              54 |              12 |

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

## Interesting Aggregates

|   Year |   Avg Rating | Recipe Count   |
|-------:|-------------:|:---------------|
|   2008 |         4.60  | 21,770         |
|   2009 |         4.62 | 16,077         |
|   2010 |         4.64 | 8,551          |
|   2011 |         4.68 | 5,264          |
|   2012 |         4.68 | 3,667          |
|   2013 |         4.66 | 2,715          |
|   2014 |         4.62 | 733            |
|   2015 |         4.65 | 212            |
|   2016 |         4.47 | 129            |
|   2017 |         4.43 | 172            |
|   2018 |         4.78 | 98             |

The table above provides an overview of the average recipe ratings and the number of recipes submitted each year. The `Avg Rating` column shows the average rating of recipes submitted in that year, while the `Recipe Count` column indicates how many recipes were submitted. 

This table adds to the visual analysis by offering detailed insights into the trends. It answers part of the initial question by showing that recipe submissions vary greatly over the years, but average recipe ratings do not. 

## Imputation

After investigation, I found that the only two columns with missing values were `rating` and `description`. I did not perform any imputation since missing target values would have to be dropped in modeling and missing values in the `description` column were already filled with `np.nan` during data cleaning. 

# Framing a Prediction Problem

For the prediction problem, I am trying to predict the average rating of a recipe. This is a regression problem because the response variable, `rating`, is continuous. I chose this variable because it may lead to insights about what types of recipes users prefer, allowing recipe creators to reach their target audience and recipe users to find what recipes they like best. 

The metric I chose to evaluate my model is mean absolute error (MAE). MAE directly measures the average absolute difference between predicted and actual ratings in the same unit as the target, `rating`, making it easily interpretable. Unlike mean squared error (MSE), MAE is less sensitive to outliers. Since we're predicting ratings on a fixed scale (1.0 to 5.0), MAE is a good choice to minimize the average deviation. 

At the time of prediction, the variables I will be using include `n_steps` (the number of steps), `n_ingredients` (the number of ingredients),`minutes` (preparation time in minutes), `year_submitted` (the year the recipe was submitted), and `calories` (the amount of calories).

# Baseline Model

For the baseline model, I used a **linear regression** model to predict the **average rating** of recipes based on the number of ingredients (`n_ingredients`) and the number of steps (`n_steps`). Both of these features are **quantitative** variables, so I performed a **log transformation** using a `FunctionTransformer` to address any skewness in the data. This helps make sure that the features are distributed more normally, which can improve model performance. 

The model was evaluated using **mean absolute error (MAE)**. The **training MAE** was 0.458, and the **testing MAE** was 0.464. This suggests that the model is performing similarly on both the training and testing data, meaning it is neither underfitting nor overfitting.

While the model's performance was reasonable, I do not consider it to be "good" because the features used were very basic and lacked meaningful transformations. I also feel they do not encompass the most meaningful features in the prediction of average rating. The model could be improved by adding more features or using feature engineering techniques to capture more complex patterns in the data. 

# Final Model

For the final model, I added several features to capture more complexity in the data and improve the predictions:
- **Polynomial features:** I applied **polynomial features** for `n_steps` and `minutes` to capture nonlinear relationships between these features and the target variable, `rating`. For example, the preparation time might not linearly correlate with ratings -- longer or shorter recipes could have more intricate impacts on the rating. Polynomials of this feature will allow the model to account for this.
- **Log transformation:** I log-transformed the feature `n_ingredients` to account for skewness and outliers. Since the number of ingredients can vary greatly between recipes, this makes the distribution more normal, allowing the model to better learn from this feature.
- **One-hot encoding for `year_submitted`:** Since the submission year is a **categorical** feature, I used **one-hot encoding** to turn this into multiple binary columns. This allows the model to treat each year as a distinct category, which may help in understanding trends over time.

The modeling algorithm I used is **linear regression**, which works well with prediction of continuous variables. I used **grid search cross-validation (`GridSearchCV`)** to tune the hyperparameter of the **degree of polynomial**, to find the optimal complexity of the model. After running the grid search, I found the best degree for the polynomial and fitted the model with those optimal parameters.

In terms of performance, the training MAE of the final model was 0.456, only slightly better than that of the baseline model, and the testing MAE did not change. The slight improvement in the training MAE comes from the added complexity of the new features and polynomial degree to reduce prediction errors and make the model more accurate. My hypothesis, after testing several different combinations of features, is that the testing error did not change because the features barely affect the average rating. This is potentially due to the imbalance of average rating values (most being above 4), as well as the lack of disparity between them. 