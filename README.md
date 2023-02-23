# Recipe-Analysis-EDA
A project for DSC 80 at UCSD


# Introduction and Question Identification
The dataset we used mainly consists of the details of a recipe and the ratings of the recipes. Our analysis is mainly centered on the question: “Will the sugar level affect the ratings of the recipes”. The question brings our attention, as the trend of preferring healthy food appears in nowadays society. The dataset contains 234429 rows. The details description of the relevant columns are as follows:
- “name”: recipe name
- “id”: recipe id
- “n_steps”: number of steps for the recipes
- “description”: description of the recipes
- “n_ingredients”: number of ingredients the recipe will use
- “ratings”: ratings given by the reviewer
- “review”: text-formed reviews
- “avg_rating”: average rating per recipes
- “calories (#)”: amount of calories contained in the recipe
- “'total fat (PDV)”: amount of fat contained in the recipe (PDV stands for percentage of daily value)
-  “sugar (PDV)”: amount of sugar in the recipe
- “sodium (PDV)”: amount of sodium in the recipe
- “protein (PDV)”: amount of protein in the recipe
- “saturated fat (PDV)”: amount of saturated fat in the recipe
- “carbohydrates (PDV)”: amount of carbohydrates in the recipe


# Data Cleaning
Following the instruction, we merged the two datasets, fill all ratings of 0 with np.nan, and create a column containing the average rating per recipe. 
Besides the required steps, we also create a separate column for every unique nutrition component of the 'nutrition' column and converted the date types of these columns to float64.
We noticed that the minutes column contains extremely large outliers up to 1 million. For a recipe, a duration of more than 1000 minutes is highly unlikely and unrealistic. So we droped the recipes in our dataset with 1000 or more minutes. We removed outliers mainly because later we need to identify the missingness mechanism that includes computing the means, and we don't want the extreme outlier to affect our analysis and inference. For the rest of the columns, although there exists outliers, but generally these outliers are not as extreme as the 'minutes' column's outliers. For example, the maximum number of steps is 93 and that is somewhat reasonable. So we decided to keep these data instead of removing them.
(图) (图)

# Univariate Analysis
(图 rating)
This graph shows the distribution of the cleaned column of minutes (without outliers). The graph shows that the majority of recipes’ times are controlled under 120 minutes. Half of the recipes take less than 35 minutes.  This shows that a recipe with a time higher than 1000 minutes will definitely be counted as an impossible recipe.

(图 minutes)
The graph is a histogram that shows the distribution of recipes’ ratings. The graphs represent a left skewed distribution, which means the ratings of recipes tend to be high ratings. Most recipes have a 5 rating.


# Bivariate Analysis




# Interesting Aggregates




# NMAR Analysis
(图)
The data we analyzed has four columns with non-trivial missing values: "description", "rating", "review", "avg_rating". Since "avg_rating" is derived from taking the mean of "rating" for each "recipe_id", we can say "avg_rating" column is missing by design. So we focued on identifying the missingness mechanism of "description", "rating", and "review". We argued that the "review" columns is likely to be "NMAR". After reasoning about the data generating process, we came up with two explanations: 
1) People that rate recipes sometime are reluctant to write review because they are lazy and write review cost time. 
2) People that don't write review maybe less educated or even illiterate. They don't know how to organizing their opinions into sentences. If we instead have access to the education level of all the reviewers, we might make the "review" column MAAR.


# Missingness Dependency




# Hypothesis Testing