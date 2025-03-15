# Recipe Review Analysis 
## This is the DSC80 Final Project for UCSD

## Overview
My name is Nafeesa Karim, and this project aims to analyze the relationship between the complexity of online recipes and thier ratings. Specifically, do recipes with a longer preperation time or more steps tend to have lower ratings? Can the ratings of a recipe be predicted by it's complexity? 

## Introduction
For many college students, moving off-campus without a dining plan for the first time means having to learn to cook. For most students, and people in general, searching online for simple but delicious recipes is the only way to figure out how to make meals. However, there are so many recipes online for so many types of meals, and it can be difficult to sort through all of them. Recipes take too long or have too many steps may be harder for people to pick up, but an oversimplified recipe may sacrifice how good the meals turns out. **I wanted to see whether there was a relationship between the average ratings of a recipe and how complicated it was, where how complicated a recipe is may depend on the number of steps and preperation time.**

The dataset used for this project consists of two files: `RAW_recipes.csv`, which gives information on different recipes, and `interactions.csv`, which gives information of ratings and reviews left by users on recipes. Both files are from food.com. 

The `RAW_recipes.csv` file has 83783 rows corresponding to unqiue recipes, and the following columns:
`name`: The title of each recipe
`id`: a unique id for each recipe
`minutes`: the number of minutes each recipe takes
`contributor`: numbers corresponding to the contributor of each recipe
`submitted`:the date in M/D/Y when each recipe was submitted.
`tags`: the tags on each recipe, as a list.
`nutrition`: a list containing the calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. 
`n_steps`: number of steps
`steps`: a list containing each step
`description`: a description of each recipe
`ingredients`: a list of each ingredient
`n_ingredients`: the total number of ingredients

The `interactions.csv` file has 731928 rows and the following columns:
`user_id`: the id of the user leaving the review
`recipe_id`: the id of the recipe for each review
`date`: date the review was left in M/D/Y
`rating`: The rating left (out of 5).
`review`: review 

## Data Cleaning and Analysis

### Data Cleaning 
The recipe and interactions datasets were merged with a left merge on `id` and `recipe_id`, since the ids of each recipe are common to both datasets and can help match ratings to the recipes themselves. 

Next, the ratings with 0 were filled with `np.nan`. These are the recipes with reviews but not given a rating, and thus cannot be considered with working with the recipes that actually do have ratings. 

A new column with the average rating per recipe was added to recipe dataset. 

The values in the `nutrition` column were converted from strings to numerics and each split into thier own columns. Both `average_rating` and `minutes` were converted to numerics, and the values of 0 in `minutes` were converted to `np.nan`.

The cleaned up data left a dataframe with 83782 rows and 20 columns

***(show head of data set)***

### Univariate Analysis
The univariate analysis looked at the distribution of ratings, giving a sense of how often certain ratings were give. The plot reveals that the higher the rating, the most often it was given, with 5 star ratings given substantially more often than other ratings.

<iframe
  src="assets/rating_dist.html"
  width="400"
  height="400"
  frameborder="0"
></iframe>


### Bivariate Analysis
The bivariate analysis looked at the average rating based on the number of minutes the recipe took. There are a coupleof outliers, but it looks that the recipes with a longer preperation time had higher ratings. 

<iframe
  src="assets/prep_time_vs_ratings.html"
  width="400"
  height="400"
  frameborder="0"
></iframe>

## Aggregates
The pivot table below looks at the average rating for different categories of cooking times. For the most part, recipes with lower cooking times tend to have higher ratings. 

<iframe
  src="assets/rating_vs_time_categories.html"
  width="400"
  height="400"
  frameborder="0"
></iframe>


## Data Missingness
Analyzing the data set, I concluded that it is very likely that there is NMAR for the review column. This is due to the fact that not everyone will leave a review; in fact, usually users with more extreme experiences will leave a review.




## Hypothesis Testing
A permutation test was used to determine whether there was a relationship between cooking time and the average rating. 

**Null Hypothesis:** There is no significant difference in average ratings between different preperation times

**Alternative Hypothesis:** Recipes with longer preperation times

**Test Statistic:** The difference in means in average ratings between recipes above and below the median preperation times. 

The recipes were split into two groups: those with a "shorter" cooking time, taking less than or equal to 30 minutes, and those with a "longer" cooking time, taking over 30 minutes. The permutation. The difference in the average for wach group was calculated. Then the minutes column was randomly shuffled, and the difference in averages was calculated again with the new shuffled data. This process was repeated for 1000 simulations.

***insert chart***

A p-value was calculated by looking at the proportion that are as or more extreme as the observed value. I got a **p-value of 0.0001**

since the p-value is less than a significance value of 0.05, the **null hypothesis is rejected**. There is a difference in average ratings with shorter and longer cook times, meaning that cook time may affect recipe ratings. 

## Prediction Problem
I also wanted to see if I could predict the average rating that a recipe would get based on overall complexity. The cooking time is one characteristic of complexity, but the number of actual steps can also contribute to how complicated a recipe is. 

This would be a **regression problem** because

I would use `average_rating` as a response variable because it corresponds to a user's satisfaction after trying a recipe, and is possibly affected by the cooking time.

The features included to qunaitfy "complexity" will be `minutes`, `_steps`, and `n_ingredients`. Usually, recipes tend to be more complex if it takes longer and has a lot of steps to follow. They can also be more complicated if more ingredients are needed. These are all information we would have at the time of prediction.

## Baseline Model
The baseline model is a linear regression model using the three columns mentioned above as the features, and `average_rating` as the response variable. 

The data was split into two groups, one for training and one for testing. The quality of the model's predictins was measured via the root mean squared error (RMSE). 

## Final Model
Our final model used `Cooking Time` and `n_ingredients` as the features, which are both numerical values that didn't need categorial encoding. However, since one is discrete and one is continuous, they needed to be normalized to the same scale, so a standardizing transformation needs to be applied.

`Cooking Time` was used because looking at our hypothesis model, there is a relationship between cooking time and average rating, which can help predict rating.

`n_ingredients` was used because, similarily, I assume there is a relationship between the number of ingredients and the rating.


I used a RMSE to asses the model's accuracy because RMSE looks at the average discrepancy between predicted and actual ratings. I got an **RMSE = 0.876**. This means the predicted ratings usually deviated by 0.876 points from the actual ratings. 

## Fairness Analysis
