# Recipes and Ratings EDA - Is there a coorelation between the amount of protein in a recipe and the Average Rating or Cooking Time

By: Ethan Deng, Jason Gu

# Introduction:
Recently there have been more bodybuilding and strength influencers on the rise, leading to an increase in gym culture and people striving to improve their physique. With this in mind, it's important to eat high-protein foods to build muscle after a hard workout, so in our project, we're curious to find out if recipes that are high in protein are also the ones that people seem to like more. We're also interested in seeing if the time it takes to cook these protein-rich meals affects how much people enjoy them. After all, we all want delicious food that's also good for our muscles, but if it takes too long to make, we might not be that keen on cooking it.


The dataset we are working with is from food.com and contains recipes and reviews that have been collected since 2008. This dataset is divided into two data frames, recipes and ratings. We are mainly interested in the recipes data frame, where we will be looking at the nutrition and cooking time. However, we will merge the two data frames to get the average ratings for each recipe and use that for our analysis.

The recipe data frame has 83782 rows, meaning that there are 83782 unique recipes. Meanwhile, the reviews data frame has 731927 rows, where each review is from a person that submitted a review of a recipe from the recipes data frame. 



### This is what the recipes and reviews dataframe looks:

```python
reviews = pd.read_csv('food_data/RAW_interactions.csv')
reviews
```

|    user_id |   recipe_id | date       |   rating | review                                             |
|-----------:|------------:|:-----------|---------:|:---------------------------------------------------|
|    1293707 |       40893 | 2011-12-21 |        5 | So simple, so delicious! Great for chilly fall eve |
|     126440 |       85009 | 2010-02-27 |        5 | I made the Mexican topping and took it to bunko.   |
|      57222 |       85009 | 2011-10-01 |        5 | Made the cheddar bacon topping, adding a sprinklin |
|     124416 |      120345 | 2011-08-06 |        0 | Just an observation, so I will not rate.  I follow |
| 2000192946 |      120345 | 2015-05-10 |        2 | This recipe was OVERLY too sweet.  I would start o |
|     468945 |      134728 | 2008-02-20 |        0 | Made my own buttermilk w/ vinegar and milk.  Used  |
|     255338 |      134728 | 2008-04-11 |        5 | First time using liquid smoke in a recipe. Made th |
|    1171894 |      134728 | 2009-04-21 |        5 | MMMMM! This is so good! I actually soaked the chic |
|     217118 |      200236 | 2008-04-18 |        5 | This was a lovely Morrocan style dish. I halved th |
| 2000049093 |      200236 | 2015-03-08 |        5 | I love lamb stew and usually make an Irish version |

```python
recipes = pd.read_csv('food_data/RAW_recipes.csv')
recipes
```
| name                                    |     id |   minutes |   contributor_id | submitted   | tags                                               |
|:----------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------|
| 1 brownies in the world    best ever    | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', ' |
| 1 in canada chocolate chip cookies      | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine',  |
| 412 broccoli casserole                  | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', ' |
| millionaire pound cake                  | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation |
| 2000 meatloaf                           | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'pre |
| 5 tacos                                 | 500166 |        20 |          2549237 | 2013-05-13  | ['weeknight', '30-minutes-or-less', 'time-to-make' |
| 50 chili   for the crockpot             | 501028 |       345 |          2628680 | 2013-05-28  | ['course', 'main-ingredient', 'cuisine', 'preparat |
| blepandekager   danish   apple pancakes | 503475 |        50 |           128473 | 2013-07-08  | ['danish', '60-minutes-or-less', 'time-to-make', ' |
| lplermagrone                            | 522861 |        50 |           135470 | 2015-07-25  | ['60-minutes-or-less', 'time-to-make', 'course', ' |
| lplermagrone  herdsman s macaroni       | 457136 |        40 |            65502 | 2011-05-23  | ['60-minutes-or-less', 'time-to-make', 'course', ' |

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


Here we take a look at the distribution of how many grams of protein is in the recipes, the distribution average ratings, and the distribution of cooking time all on their own bar charts.


<iframe src="assets/smaller_distributions.html" width=800 height=1000 frameBorder=0></iframe>

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
<iframe src="assets/protein_vs_cooking_time.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/protein_vs_average_rating.html" width=800 height=600 frameBorder=0></iframe>

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

print(grouped[['protein', 'minutes']].head().to_markdown(index=False))

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
