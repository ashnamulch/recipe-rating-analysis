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

First 5 rows of the cleaned table:

| name                                    |     id |   minutes |   contributor_id | submitted           | tags                                                                                                                                                                                                                                                                                                                                                             | nutrition                                   |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                                                                             |   n_ingredients |   rating |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |   year_submitted |   month_submitted |
|:----------------------------------------|-------:|----------:|-----------------:|:--------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------|----------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|---------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|-----------------:|------------------:|
| 1 brownies in the world    best ever    | 333281 |        40 |           985201 | 2008-10-27 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                                                                                      | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]    |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 |        4 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |             2008 |                10 |
| 412 broccoli casserole                  | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                                                                                             | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]   |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |        5 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |             2008 |                 5 |
| 2000 meatloaf                           | 475785 |        90 |          2202916 | 2012-03-06 00:00:00 | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                                                                                           | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]  |        17 | ['pan fry bacon , and set aside on a paper towel to absorb excess grease', 'mince yellow onion , red bell pepper , and add to your mixing bowl', 'chop garlic and set aside', 'put 1tbsp olive oil into a saut pan , along with chopped garlic , teaspoons white pepper and a pinch of kosher salt', 'bring to a medium heat to sweat your garlic', 'preheat oven to 350f', 'coarsely chop your baby spinach add to your heated pan , stir frequently for approximately 5 min to wilt', 'add your spinach to the mixing bowl', 'chop your now cooled bacon , and add it to the mixing bowl', 'add your meatloaf mix to the bowl , with one egg and mix till thoroughly combined', 'add your goat cheese , one egg , 1 / 8 tsp white pepper and 1 / 8 tsp of kosher salt and mix till thoroughly combined', 'transfer to a 9x5 meatloaf pan , and cook for 60 min or until the internal temperature is at least 160f', 'let stand for 5min', 'melt 1tbsp unsalted butter into a frying pan , and cook up to three eggs at a time', 'crack each egg into a separate dish , in order to prevent egg shells from reaching the pan , then add salt and pepper to taste', 'wait until the egg whites are firm looking , but slightly runny on top before flipping your eggs', 'after flipping , wait 10~20 seconds before removing each egg and placing it over your slices of meatloaf'] | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese.                                                                                                                                                                         | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |        5 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |             2012 |                 3 |
| 5 tacos                                 | 500166 |        20 |          2549237 | 2013-05-13 00:00:00 | ['weeknight', '30-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'occasion', 'main-dish', 'beef', 'vegetables', 'easy', 'diabetic', 'dinner-party', 'kid-friendly', 'stove-top', 'dietary', 'comfort-food', 'inexpensive', 'ground-beef', 'meat', 'greens', 'lettuces', 'tomatoes', 'taste-mood', 'equipment', '3-steps-or-less'] | [249.4, 26.0, 4.0, 6.0, 39.0, 39.0, 0.0]    |         5 | ['cook meat', 'add taco seasoning', 'place meat into taco shells / tortillas', 'top with tomatoes , onions , lettuce , salsa and cheese', 'boil corn cobs 5-7 minutes']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | costs about $5.00 to make.                                                                                                                                                                                                                                                                                                                                                        | ['ground beef', 'taco seasoning', 'taco shells', 'lettuce', 'tomatoes', 'onion', 'salsa', 'cheddar cheese', 'corn cobs']                                                                                                                |               9 |        4 |      249.4 |          26 |       4 |        6 |        39 |              39 |               0 |             2013 |                 5 |
| blepandekager   danish   apple pancakes | 503475 |        50 |           128473 | 2013-07-08 00:00:00 | ['danish', '60-minutes-or-less', 'time-to-make', 'course', 'cuisine', 'preparation', 'pancakes-and-waffles', 'breakfast', 'scandinavian', 'european']                                                                                                                                                                                                            | [358.2, 30.0, 62.0, 14.0, 19.0, 54.0, 12.0] |        10 | ['beat the eggs lightly and add the milk', 'combine the flour , sugar and salt', 'stir the flour mixture into the egg mixture , stirring in the cup of cream as you mix', 'fry the apple slices in butter in a skillet', 'preheat oven to 500 degree', 'cover the bottom of an oven-proof baking dish , or heavy skillet , with apples', 'pour the batter over slices and bake in a preheated 500 oven', 'when nearly done , remove from oven and sprinkle here and there with a mixture of sugar and cinnamon to taste', 'place dabs of butter on the pancake and return to oven until browned', 'just before serving , sprinkle with lemon juice , and cut into triangles']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | this recipe has been posted here for play in zwt9 - scandinavia.  this recipe was found at website: mindspring.com - christian's danish recipes.                                                                                                                                                                                                                                  | ['eggs', 'milk', 'flour', 'sugar', 'salt', 'cream', 'apples', 'butter', 'cinnamon', 'lemon, juice of']                                                                                                                                  |              10 |        5 |      358.2 |          30 |      62 |       14 |        19 |              54 |              12 |             2013 |                 7 |

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



# Framing a Prediction Problem

# Baseline Model

# Final Model