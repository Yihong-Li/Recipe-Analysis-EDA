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
<iframe src="assets/outlier.html" width=800 height=600 frameBorder=0></iframe>

Here is our dataframe after processing, we didn't include the 'steps' columns as the string is too long and it's not helpful to our analysis, we will use our cleaned data throughout later analysis:
| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        | nutrition                                    |   n_steps | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                    |   n_ingredients |          user_id |   recipe_id | date       |   rating | review                                                                                                                                                                                                                                                                                                                                           |   avg_rating |   calories (#) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------:|---------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |        10 | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |            4 |          138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |            5 |          595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |            5 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                             |                                              |           |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                  |             |            |          | The photos you took (shapeweaver) inspired me to make this recipe and it actually does look just like them when it comes out of the oven.                                                                                                                                                                                                        |              |                |                   |               |                |                 |                       |                       |
|                                      |        |           |                  |             |                                                                                                                                                                                                                             |                                              |           |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                  |             |            |          | Thanks so much for sharing your recipe shapeweaver. It was wonderful!  Going into my family's favorite Zaar cookbook :)                                                                                                                                                                                                                          |              |                |                   |               |                |                 |                       |                       |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |      1.19628e+06 |      306168 | 2009-04-13 |        5 | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept saying how delicious it was. I was I could have gotten to try it.                                                                                                                                                                               |            5 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 | 768828           |      306168 | 2013-08-02 |        5 | Loved this.  Be sure to completely thaw the broccoli.  I didn&#039;t and it didn&#039;t get done in time specified.  Just cooked it a little longer though and it was perfect.  Thanks Chef.                                                                                                                                                     |            5 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |


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

|   minutes |   n_steps |
|----------:|----------:|
|   74.2943 |  10.5963  |
|   72.0388 |  10.6248  |
|   67.9626 |   9.96381 |
|   61.1686 |   9.5531  |
|   58.4639 |   9.94848 |


# Assessment of Missingness
## NMAR Analysis
The data we analyzed has four columns with non-trivial missing values: "description", "rating", "review", "avg_rating". Since "avg_rating" is derived from taking the mean of "rating" for each "recipe_id", we can say "avg_rating" column is missing by design. So we focued on identifying the missingness mechanism of "description", "rating", and "review". We argued that the "review" columns is likely to be "NMAR". After reasoning about the data generating process, we came up with two explanations: <br>
1) People that rate recipes sometime are reluctant to write review because they are lazy and write review cost time. <br>
2) People that don't write review maybe less educated or even illiterate. They don't know how to organizing their opinions into sentences. If we instead have access to the education level of all the reviewers, we might make the "review" column MAAR.
|                     |     0 |
|:--------------------|------:|
| name                |     1 |
| id                  |     0 |
| minutes             |     0 |
| contributor_id      |     0 |
| submitted           |     0 |
| tags                |     0 |
| nutrition           |     0 |
| n_steps             |     0 |
| steps               |     0 |
| description         |   113 |
| ingredients         |     0 |
| n_ingredients       |     0 |
| user_id             |     1 |
| recipe_id           |     1 |
| date                |     1 |
| rating              | 14807 |
| review              |    56 |
| avg_rating          |  2722 |
| calories (#)        |     0 |
| total fat (PDV)     |     0 |
| sugar (PDV)         |     0 |
| sodium (PDV)        |     0 |
| protein (PDV)       |     0 |
| saturated fat (PDV) |     0 |
| carbohydrates (PDV) |     0 |


## Missingness Dependency
We selected the "description" columns in our dateset with 113 missing values. Our pre-defined significance level is 5%. We performed permutation tests to determine whether the missingness of "description" column depends on "minutes". After graphing out the distribution of minutes for both missing and non-missing "description", we decided to use ks-statistic as our test statistic and the resulting p-value is 0.239. Thus, we fail to reject the null hypothesis - "the distribution of 'minutes' is same when 'description' is missing and when 'description' is not missing". The missingness of "description" is not dependent on "minutes". (we also tried absolute difference in means and got the same result). 
<iframe src="assets/missing-minutes-des.html" width=800 height=600 frameBorder=0></iframe>
We also performed permutation tests to determine whether the missingness of "description" columns is dependent on "n_ingredients". Similarly, we used ks-statistic as our test statistic and the resulting p-value is 0.0154. Based on our 5% significance level, we rejected the null hypothesis - "the distribution of 'n_ingredients' is same when 'description' is missing and when 'description' is not missing". The missingness of "description" is dependent on "n_ingredients". "description" is MAR.
<iframe src="assets/missing-ingredients-des.html" width=800 height=600 frameBorder=0></iframe>


# Hypothesis Testing
We choose the test statistic as the absolute mean difference between the ratings regarding zero sugar level recipes and the ratings regarding recipes containing sugar. We choose the absolute difference because both ratings and sugar amount are numerical data and our alternate hypothesis doesn’t have a direction. We set the significance level to 0.05, as we only run  the test 1000 times. We performed the hypothesis test and acquired a p-value of 0.009. Therefore, we reject the null hypothesis and conclude that ratings of non-sugar recipes have a different distribution with ratings of recipes containing sugar.