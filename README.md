# Recipes and Ratings EDA - Is there a coorelation between the amount of protein in a recipe and the Average Rating or Cooking Time

**Authors**: Ethan Deng, Jason Gu

# Introduction:
Recently there have been more bodybuilding and strength influencers on the rise, leading to an increase in gym culture and people striving to improve their physique. With this in mind, it's important to eat high-protein foods to build muscle after a hard workout, so in our project, we're curious to find out if recipes that are high in protein are also the ones that people seem to like more. We're also interested in seeing if the time it takes to cook these protein-rich meals affects how much people enjoy them. After all, we all want delicious food that's also good for our muscles, but if it takes too long to make, we might not be that keen on cooking it.

The dataset we are working with is from food.com and contains recipes and reviews that have been collected since 2008. This dataset is divided into two data frames, recipes and ratings. We are mainly interested in the recipes data frame, where we will be looking at the nutrition and cooking time. However, we will merge the two data frames to get the average ratings for each recipe and use that for our analysis.

The recipe data frame has 83782 rows, meaning that there are 83782 unique recipes. Meanwhile, the reviews data frame has 731927 rows, where each review is from a person that submitted a review of a recipe from the recipes data frame. 



### This is a description of what the recipes dataframe contains (83782 rows):

|    Column  |  Desciption |
|-----------:|------------:|
|    'name' |       Recipe name |
|     'id' |       Recipe ID |
|      'minutes' |       Minutes to prepare recipe |
|     'submitted' |      User ID who submitted this recipe |
| 'tags' |      Food.com tags for recipe |
|     'nutrition' |      Nutrition information in this form [calries (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
|     'n_steps' |      Number of steps in the recipe |
|    'steps' |      Step by step instructions to follow |
|     'description' |      A description of what the recipe makes |

### This is a description of what the reviews dataframe contains (731927 rows):

|    Column  |  Desciption |
|-----------:|------------:|
|    'user_id' |       User ID |
|     'recipe_id' |       Recipe ID |
|      'date' |       Date of interaction|
|     'rating' |      Rating (1-5) |
| 'review' |      Review text |

# Data Cleaning

1. We merged the two data frames on the 'recipe_id' column from 'reviews' and the 'id' column from 'recipes' to assign each rating from the reviews data frame onto each recipe.

2. We grouped by the 'recipe_id's and took the mean of the ratings for all reviews on each recipe to get an average rating for each recipe.

3. We assigned this series of average ratings onto a new column in the recipes data frame.

4. We also had to convert the nutrition column from a string into a list. This then makes it possible to extract the grams of protein from that column and add a new column called 'protein' to easily identify how much protein is in each recipe.

5. There were some recipes that had no average rating (or an average rating of 0, which are invalid) and some recipes that didn't have a description, so we got rid of those rows and saved it to a new dataframe called recipes_nona to better examine the data that we do have. There were about 2600 rows with NA values, but thankfully this didn't impact the size of our data frame that much.

### Here is what our recipes data frame now looks like with no 'NA' values, and some columns have been removed for an easier viewing experience on the variables of interest.


| name                                 |     id |   minutes |   contributor_id | submitted   | nutrition                                     |   average_rating |   protein |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------|-----------------:|----------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |                4 |         3 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |                5 |        13 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |                5 |        22 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |                5 |        20 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |                5 |        29 |

# Univariate Analysis

### Here we take a look at the distribution of how many grams of protein is in the recipes, the distribution average ratings, and the distribution of cooking time all on their own bar charts.


<iframe src="assets/smaller_distributions.html" width=800 height=1000 frameBorder=0></iframe>

Here above we had to remove some outliers in the protein and time columns because there were some recipes with ridiculously unreal amounts of protein and time taken, which were clearly not real recipes. We excluded the outliers by only including values within 2 standard deviations within the mean. But as we can see, most recipes have a fairly low amount of protein and take about 30-50 minutes to prepare, and as the amount of protein in a recipe increases, they appear less frequently.
Also we can see that most recipe ratings are a 4 and above


# Bivariate Analysis

### Here we look at two scatter plots to see the relationship between protein and average ratings among the recipes. As well as protein and cooking time.

<iframe src="assets/protein_vs_cooking_time.html" width=800 height=600 frameBorder=0></iframe>

When looking at the scatter plot of Protein vs Average Rating, there isn't much of a relationship between the two variables, but we can see that most recipes have a high average rating and low protein, as most of the dots are clumped into the top left

<iframe src="assets/protein_vs_average_rating.html" width=800 height=600 frameBorder=0></iframe>

When looking at the scatter plot of Protein vs Average Rating, there also isn't much of a relationship, but it is clear that most recipes have a shorter cooking time and lower protein.


# Interesting Aggregates

### Here we are aggregating the recipes by average rating, and seeing how the amount of protein and cooking time vary among each rating group.


| ('rating_group', '')   |   ('protein', 'mean') |   ('protein', 'median') |   ('protein', 'std') |   ('minutes', 'mean') |   ('minutes', 'median') |   ('minutes', 'std') |
|:-----------------------|----------------------:|------------------------:|---------------------:|----------------------:|------------------------:|---------------------:|
| 1-2                    |               27.2464 |                      14 |              28.8051 |               60.6933 |                      40 |              76.5207 |
| 2-3                    |               30.5448 |                      19 |              30.0176 |               62.3437 |                      40 |              81.769  |
| 3-4                    |               31.0934 |                      20 |              29.7814 |               58.9369 |                      35 |              78.132  |
| 4-5                    |               28.6202 |                      17 |              29.3303 |               54.5229 |                      35 |              70.8765 |

Here we can get a better look at how protein and cooking time vary between the ratings groups. From this table, there seems to be little to no differences for protein among the rating groups, as they fluctuate minimally. However, it is pretty clear that the recipes in the 4-5 rating group have a mean, meadian, and standard deviation of cooking time. This makes sense because most people would rather not spend a longer time cooking if they don't have to.

| rating_group   |    0-30 |   30-60 |   60-120 |    120+ |
|:---------------|--------:|--------:|---------:|--------:|
| 1-2            | 22.6831 | 26.9227 |  32.3457 | 39.287  |
| 2-3            | 24.2185 | 32.2783 |  33.6454 | 46.0409 |
| 3-4            | 25.2867 | 33.1707 |  36.7743 | 42.1723 |
| 4-5            | 22.7715 | 31.8557 |  34.2425 | 39.7874 |

In this table, we can see that as the cooking time increases for the recipe, the protein also increases. This makes sense because typically a longer cooking time means a bigger portion of food being prepared. However, within the cooking times, it seems that foods with a 2-3 or 3-4 rating have a little more protein than ratings between 1-2 and 4-5. What we can deduce from this is that protein may be slightly less favorable for most people.

# NMAR Analysis
We understand that “Not Missing at Random” (NMAR) indicates that there is a relationship between the propensity of a value to be missing and its value. We think that the missingness in the average_rating is potentially NMAR. We believe that the missingness in average_rating has to do with the length of the recipe. The longer a recipe is and the longer it takes to finish the recipe, it is potentially less likely for the recipe to be made, causing the recipe to have fewer ratings or even missing ratings. Furthermore, we believe that it is more likely for recipes that are either really good or really bad to receive ratings. It is possible that the missing ratings are due to the fact that people did not feel anything special about the recipe. Due to the fact that there may exist a cause-and-effect relationship to these missing values, NMAR would therefore be a valid indication. 

# Missingness dependency
We wanted to test if there is a correlation between the missingness in average_rating in comparison to the minutes (the minutes it takes to cook a certain recipe). The average_rating values are likely to be missing because there were no reviews for these recipes. One of the reasons is that we did a left merge with recipes and reviews to get the average rating column, where recipes are on the left, so recipes that had no reviews left for them were calculated to be NaN. Based on what we can see below in the plot, as well as our permutation test, there appears to be a correlation between the cooking time and the missingness of the average_rating column. 

[insert graph here]


Seeing that the p-value is below 0.05, this means that there is a correlation between the cooking time and the missingness of the average_rating column. This makes sense because recipes that take a long time to cook are less likely to be made and therefore won't have a rating left for them. This means that the data is NMAR because the missingness of a rating is dependent on the cooking time for the recipe.


## Test for correlation between protein content and average rating:
#### Null Hypothesis (H0): There is no correlation between protein content and average rating.
#### Alternative Hypothesis (Ha): There is a correlation between protein content and average rating.
Observed Pearson correlation: 0.18592457955173802
P-value from the permutation test: 0.0


## Test for correlation between protein content and cooking time:
#### Null Hypothesis (H0): There is no correlation between protein content and cooking time.
#### Alternative Hypothesis (Ha): There is a correlation between protein content and cooking time.
Observed Pearson correlation: -0.02273678966808098
P-value from the permutation test: 0.0

Given the results above, it seems as though there is a very weak NEGATIVE coorelation between the amount of protein in food and the rating of the food. Moreover, there is also a very weak POSITIVE coorelation between the amount of protein and the minutes to prepare the food.


```python

```
