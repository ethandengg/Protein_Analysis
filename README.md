# Recipes and Ratings EDA - Is there a coorelation between the amount of protein in a recipe and the Average Rating or Cooking Time

By: Ethan Deng, Jason Gu

```python
import pandas as pd
import numpy as np
import os
import ast
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import pearsonr
""
import plotly.express as px
pd.options.plotting.backend = 'plotly'
```

# Introduction:
Recently there have been more bodybuilding and strength influencers on the rise, leading to an increase in gym culture and people striving to improve their physique. With this in mind, it's important to eat high-protein foods to build muscle after a hard workout, so in our project, we're curious to find out if recipes that are high in protein are also the ones that people seem to like more. We're also interested in seeing if the time it takes to cook these protein-rich meals affects how much people enjoy them. After all, we all want delicious food that's also good for our muscles, but if it takes too long to make, we might not be that keen on cooking it.


The dataset we are working with is from food.com and contains recipes and reviews that have been collected since 2008. This dataset is divided into two data frames, recipes and ratings. We are mainly interested in the recipes data frame, where we will be looking at the nutrition and cooking time. However, we will merge the two data frames to get the average ratings for each recipe and use that for our analysis.

The recipe data frame has 83782 rows, meaning that there are 83782 unique recipes. Meanwhile, the reviews data frame has 731927 rows, where each review is from a person that submitted a review of a recipe from the recipes data frame. 



### This is what the recipes and reviews dataframe looks:

```python
reviews = pd.read_csv('food_data/RAW_interactions.csv')
reviews
```

```python
recipes = pd.read_csv('food_data/RAW_recipes.csv')
recipes
```

# Data Cleaning


### 1. We merged the two data frames on the 'recipe_id' column from 'reviews' and the 'id' column from 'recipes' to assign each rating from the reviews data frame onto each recipe.

### 2. We grouped by the 'recipe_id's and took the mean of the ratings for all reviews on each recipe to get an average rating for each recipe.

### 3. We assigned this series of average ratings onto a new column in the recipes data frame.

```python
merged_df = pd.merge(recipes, reviews, left_on='id', right_on='recipe_id', how='left')
merged_df['rating'] = merged_df['rating'].replace(0, np.nan)

avg_rating = merged_df.groupby('recipe_id')['rating'].mean()
recipes['average_rating'] = recipes['id'].map(avg_rating).fillna('NA')
recipes = recipes.replace("NA", np.nan)
```

### 4. We also had to convert the nutrition column from a string into a list. This then makes it possible to extract the grams of protein from that column and add a new column called 'protein' to easily identify how much protein is in each recipe.

```python
recipes['nutrition'] = recipes['nutrition'].apply(ast.literal_eval)
recipes['protein'] = recipes['nutrition'].apply(lambda x: x[4])
```

### 5. There were some recipes that had no average rating (or an average rating of 0, which are invalid) and some recipes that didn't have a description, so we got rid of those rows and saved it to a new dataframe called recipes_nona to better examine the data that we do have. There were about 2600 rows with NA values, so this didn't impact the size of our data frame that much.

```python
recipes_nona = recipes.dropna()
recipes_nona
```

# Univariate Analysis

<iframe src="assets/file-name.html" width=800 height=600 frameBorder=0></iframe>
Here we take a look at the distribution of how many grams of protein is in the recipes, the distribution average ratings, and the distribution of cooking time all on their own bar charts.

```python
titles = ['Protein Distribution', 'Average Rating Distribution', 'Average Time Distribution (Minutes)']

# Variables to plot
variables = ['protein', 'average_rating', 'minutes']

def remove_outliers(df, column, std_devs=2):
    if column == 'average_rating':
        std_devs=50
        mean = df[column].mean()
        std_dev = df[column].std()
    elif column == 'minutes':
        std_devs=0.1
        mean = df[column].mean()
        std_dev = df[column].std()
    else:
        mean = df[column].mean()
        std_dev = df[column].std()
    return df[(df[column] <= mean + std_devs * std_dev) & (df[column] >= mean - std_devs * std_dev)]

for var in variables:
    recipes_nona = remove_outliers(recipes_nona, var)
```

```python
# Setting up the plotting area for histograms
fig, axes = plt.subplots(3, 1, figsize=(12, 15)) 
fig.subplots_adjust(hspace=0.3, wspace=0.3)

# Plotting histograms after removing outliers
for ax, var, title in zip(axes, variables, titles):
    sns.histplot(recipes_nona[var], kde=True, ax=ax, bins=10)
    ax.set_title(title)
    ax.set_xlabel(var.capitalize())
    ax.set_ylabel('Frequency')

plt.tight_layout() 
plt.show()  # Display the histograms
```

### Here above we had to remove some outliers in the protein and time columns because there were some recipes with ridiculously unreal amounts of protein and time taken, which were clearly not real recipes. But as we can see, most recipes have a fairly low amount of protein and take about 30-50 minutes to prepare, and as the amount of protein in a recipe increases, they appear less frequently.

### Also we can see that most recipe ratings are a 4 and above


# Bivariate Analysis


Here we look at two scatter plots to see the relationship between protein and average ratings among the recipes. As well as protein and cooking time.

```python

# Calculate the 99th percentile for the 'minutes' column
percentile_99 = recipes_nona['minutes'].quantile(0.99)

# Filter the DataFrame to exclude points above the 99th percentile
filtered_recipes = recipes_nona[recipes_nona['minutes'] <= percentile_99]

# Making scatter plots using the filtered data
fig, axes = plt.subplots(2, 1, figsize=(15, 12))
fig.subplots_adjust(hspace=0.3, wspace=0.3)

# Nutrients vs Average Rating
sns.scatterplot(data=filtered_recipes, x='protein', y='average_rating', ax=axes[0], color='blue')
axes[0].set_title('Protein vs Average Rating')

# Nutrients vs Cooking Time
sns.scatterplot(data=filtered_recipes, x='protein', y='minutes', ax=axes[1], color='blue')
axes[1].set_title('Protein vs Cooking Time')

plt.tight_layout()
plt.show()

```

### When looking at the scatter plot of Protein vs Average Rating, there isn't much of a relationship between the two variables, but we can see that most recipes have a high average rating and low protein, as most of the dots are clumped into the top left

### When looking at the scatter plot of Protein vs Average Rating, there also isn't much of a relationship, but it is clear that most recipes have a shorter cooking time and lower protein.


# Interesting Aggregates


Here we are aggregating the recipes by average rating, and seeing how the amount of protein and cooking time vary among each rating group.

```python
# Define the bin edges
bin_edges = [1,2,3,4,5]
bin_labels = [f'{bin_edges[i]}-{bin_edges[i+1]}' for i in range(len(bin_edges)-1)]

# Bin the average ratings with the defined edges and labels
recipes_nona['rating_group'] = pd.cut(recipes_nona['average_rating'], bins=bin_edges, labels=bin_labels, include_lowest=True)

# Group by the new rating_group and calculate aggregates
grouped = recipes_nona.groupby('rating_group').agg({
    'protein': ['mean', 'median', 'std'],
    'minutes': ['mean', 'median', 'std']
})

grouped

```

```python
rating_bin_edges = [1,2,3,4,5]
rating_bin_labels = [f'{rating_bin_edges[i]}-{rating_bin_edges[i+1]}' for i in range(len(rating_bin_edges)-1)]

# Bin the average_rating with the defined edges and labels
recipes_nona['rating_group'] = pd.cut(recipes_nona['average_rating'], bins=rating_bin_edges, labels=rating_bin_labels, include_lowest=True)


# Define time bins (assuming 'minutes' is the column with cooking time)
time_bins = [0, 30, 60, 120, np.inf]
time_labels = ['0-30', '30-60', '60-120', '120+']

# Bin the minutes into time categories
recipes_nona['time_category'] = pd.cut(recipes_nona['minutes'], bins=time_bins, labels=time_labels, include_lowest=True)

# Create a pivot table with the binned average_rating and time_category and protein as values
pivot_table = recipes_nona.pivot_table(index='rating_group', columns='time_category', values='protein', aggfunc='mean')

pivot_table

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

```python

```
