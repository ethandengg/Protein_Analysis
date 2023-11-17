# Recipes and Ratings EDA - Is there a coorelation between the amount of protein in a recipe and the Average Rating or Cooking Time

By: Ethan Deng, Jason Gu

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
|     'n_steps' |      Number of steps in recipe |
|    'steps' |      Text for recipe steps, in order |
|     'description' |      User-provided description |

### This is a description of what the reviews dataframe contains (731927 rows):
|    Column  |  Desciption |
|-----------:|------------:|
|    'user_id' |       User ID |
|     'recipe_id' |       Recipe ID |
|      'date' |       Date of interaction|
|     'rating' |      Rating (1-5) |
| 'review' |      Review text |

# Data Cleaning

### 1. We merged the two data frames on the 'recipe_id' column from 'reviews' and the 'id' column from 'recipes' to assign each rating from the reviews data frame onto each recipe.

### 2. We grouped by the 'recipe_id's and took the mean of the ratings for all reviews on each recipe to get an average rating for each recipe.

### 3. We assigned this series of average ratings onto a new column in the recipes data frame.

### 4. We also had to convert the nutrition column from a string into a list. This then makes it possible to extract the grams of protein from that column and add a new column called 'protein' to easily identify how much protein is in each recipe.

### 5. There were some recipes that had no average rating (or an average rating of 0, which are invalid) and some recipes that didn't have a description, so we got rid of those rows and saved it to a new dataframe called recipes_nona to better examine the data that we do have. There were about 2600 rows with NA values, so this didn't impact the size of our data frame that much.

## Here is what our recipes data frame now looks like with no 'NA' values, and some columns have been removed for an easier viewing experience on the important variables.


| name                                    |     id |   minutes |   contributor_id | submitted   | tags                                               | nutrition                                      |   n_steps |   n_ingredients |   average_rating |   protein |
|:----------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------|:-----------------------------------------------|----------:|----------------:|-----------------:|----------:|
| 1 brownies in the world    best ever    | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', ' | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]       |        10 |               9 |                4 |         3 |
| 1 in canada chocolate chip cookies      | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine',  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]   |        12 |              11 |                5 |        13 |
| 412 broccoli casserole                  | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', ' | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]      |         6 |               9 |                5 |        22 |
| millionaire pound cake                  | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0]  |         7 |               7 |                5 |        20 |
| 2000 meatloaf                           | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'pre | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]     |        17 |              13 |                5 |        29 |
| 5 tacos                                 | 500166 |        20 |          2549237 | 2013-05-13  | ['weeknight', '30-minutes-or-less', 'time-to-make' | [249.4, 26.0, 4.0, 6.0, 39.0, 39.0, 0.0]       |         5 |               9 |                4 |        39 |


# Univariate Analysis


Here we take a look at the distribution of how many grams of protein is in the recipes, the distribution average ratings, and the distribution of cooking time all on their own bar charts.


<iframe src="assets/smaller_distributions.html" width=800 height=1000 frameBorder=0></iframe>

### Here above we had to remove some outliers in the protein and time columns because there were some recipes with ridiculously unreal amounts of protein and time taken, which were clearly not real recipes. But as we can see, most recipes have a fairly low amount of protein and take about 30-50 minutes to prepare, and as the amount of protein in a recipe increases, they appear less frequently.

### Also we can see that most recipe ratings are a 4 and above


# Bivariate Analysis


Here we look at two scatter plots to see the relationship between protein and average ratings among the recipes. As well as protein and cooking time.
<iframe src="assets/protein_vs_cooking_time.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/protein_vs_average_rating.html" width=800 height=600 frameBorder=0></iframe>

### When looking at the scatter plot of Protein vs Average Rating, there isn't much of a relationship between the two variables, but we can see that most recipes have a high average rating and low protein, as most of the dots are clumped into the top left

### When looking at the scatter plot of Protein vs Average Rating, there also isn't much of a relationship, but it is clear that most recipes have a shorter cooking time and lower protein.


# Interesting Aggregates


Here we are aggregating the recipes by average rating, and seeing how the amount of protein and cooking time vary among each rating group.


| ('rating_group', '')   |   ('protein', 'mean') |   ('protein', 'median') |   ('protein', 'std') |   ('minutes', 'mean') |   ('minutes', 'median') |   ('minutes', 'std') |
|:-----------------------|----------------------:|------------------------:|---------------------:|----------------------:|------------------------:|---------------------:|
| 1-2                    |               27.2464 |                      14 |              28.8051 |               60.6933 |                      40 |              76.5207 |
| 2-3                    |               30.5448 |                      19 |              30.0176 |               62.3437 |                      40 |              81.769  |
| 3-4                    |               31.0934 |                      20 |              29.7814 |               58.9369 |                      35 |              78.132  |
| 4-5                    |               28.6202 |                      17 |              29.3303 |               54.5229 |                      35 |              70.8765 |



| rating_group   |    0-30 |   30-60 |   60-120 |    120+ |
|:---------------|--------:|--------:|---------:|--------:|
| 1-2            | 22.6831 | 26.9227 |  32.3457 | 39.287  |
| 2-3            | 24.2185 | 32.2783 |  33.6454 | 46.0409 |
| 3-4            | 25.2867 | 33.1707 |  36.7743 | 42.1723 |
| 4-5            | 22.7715 | 31.8557 |  34.2425 | 39.7874 |

```python

```

```python

```

```python

```

```python

```

```python

```

```python

```
