# Recipe-Analysis-EDA
A project for DSC 80 at UCSD 
(by: Yihong Li, Zixin Wei)


# Introduction
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

# Cleaning and EDA
## Data Cleaning
Following the instruction, we merged the two datasets, fill all ratings of 0 with np.nan, and create a column containing the average rating per recipe. <br>
Besides the required steps, we also create a separate column for every unique nutrition component of the 'nutrition' column and converted the date types of these columns to float64. Then we got a column named "sugar (PDV)" which we used later for answering our interested question: “Will the sugar level affect the ratings of the recipes?” <br>
We noticed that the minutes column contains extremely large outliers up to 1 million. For a recipe, a duration of more than 1000 minutes is highly unlikely and unrealistic. So we droped the recipes in our dataset with 1000 or more minutes. We removed outliers mainly because later we need to identify the missingness mechanism that includes computing the means, and we don't want the extreme outlier to affect our analysis and inference. For the rest of the columns, although there exists outliers, but generally these outliers are not as extreme as the 'minutes' column's outliers. For example, the maximum number of steps is 93 and that is somewhat reasonable. So we decided to keep these data instead of removing them.
<iframe src="assets/outliers.html" width=800 height=600 frameBorder=0></iframe>
(图)

## Univariate Analysis
<iframe src="assets/uni-rating.html" width=800 height=600 frameBorder=0></iframe>
This graph shows the distribution of the cleaned column of minutes (without outliers). The graph shows that the majority of recipes’ times are controlled under 120 minutes. Half of the recipes take less than 35 minutes.  This shows that a recipe with a time higher than 1000 minutes will definitely be counted as an impossible recipe.

<iframe src="assets/uni-minutes.html" width=800 height=600 frameBorder=0></iframe>
The graph is a histogram that shows the distribution of recipes’ ratings. The graphs represent a left skewed distribution, which means the ratings of recipes tend to be high ratings. Most recipes have a 5 rating.


## Bivariate Analysis
<iframe src="assets/bi-minutes-rating.html" width=800 height=600 frameBorder=0></iframe>
There is a weak correlation between minutes and ratings, which shows that as minutes taken for a recipe increase, the rating of the recipe tends to decrease slightly. 


## Interesting Aggregates
This aggregated data frame shows the correlation between rating and both minutes and number of steps. From the data frame we can see a higher rating related to a lower mean of minutes taken and less steps needed for the recipes. This is significant because we discovered the correlations from the data frame.


# Assessment of Missingness
## NMAR Analysis
(图)
The data we analyzed has four columns with non-trivial missing values: "description", "rating", "review", "avg_rating". Since "avg_rating" is derived from taking the mean of "rating" for each "recipe_id", we can say "avg_rating" column is missing by design. So we focued on identifying the missingness mechanism of "description", "rating", and "review". We argued that the "review" columns is likely to be "NMAR". After reasoning about the data generating process, we came up with two explanations: <br>
1) People that rate recipes sometime are reluctant to write review because they are lazy and write review cost time. <br>
2) People that don't write review maybe less educated or even illiterate. They don't know how to organizing their opinions into sentences. If we instead have access to the education level of all the reviewers, we might make the "review" column MAAR.


## Missingness Dependency
(图) （图）
We selected the "description" columns in our dateset with 113 missing values. Our pre-defined significance level is 5%. We performed permutation tests to determine whether the missingness of "description" column depends on "minutes". After graphing out the distribution of minutes for both missing and non-missing "description"（图）, we decided to use ks-statistic as our test statistic and the resulting p-value is 0.239. Thus, we fail to reject the null hypothesis - "the distribution of 'minutes' is same when 'description' is missing and when 'description' is not missing". The missingness of "description" is not dependent on "minutes". (we also tried absolute difference in means and got the same result). 
We also performed permutation tests to determine whether the missingness of "description" columns is dependent on "n_ingredients". Similarly, we used ks-statistic as our test statistic and the resulting p-value is 0.0154. Based on our 5% significance level, we rejected the null hypothesis - "the distribution of 'n_ingredients' is same when 'description' is missing and when 'description' is not missing". The missingness of "description" is dependent on "n_ingredients". "description" is MAR.


# Hypothesis Testing
We choose the test statistic as the absolute mean difference between the ratings regarding zero sugar level recipes and the ratings regarding recipes containing sugar. We choose the absolute difference because both ratings and sugar amount are numerical data and our alternate hypothesis doesn’t have a direction. We set the significance level to 0.05, as we only run  the test 1000 times. We performed the hypothesis test and acquired a p-value of 0.009. Therefore, we reject the null hypothesis and conclude that ratings of non-sugar recipes have a different distribution with ratings of recipes containing sugar.