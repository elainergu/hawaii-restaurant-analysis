# Hawaii Restaurant Naming Analysis
DSC 80 project analyzing Hawaii restaurant pricing, ratings, and naming patterns.

### Introduction
The dataset contains Google Maps information for various locations in Hawaii. This project uses the Hawaii business metadata, which is part of a much larger dataset.

**Research Question:** Do certain naming features of Hawaii restaurants correlate with higher price levels and average ratings?

This question is relevant because restaurant names are intentional and important to how businesses reflect their style and identity. Understanding whether these naming choices relate to customer ratings or price can reveal patterns in how restaurants are presented and perceieved. 

The following dataset was originally scraped and used in the research papers [UCTopic: Unsupervised Contrastive Learning for Phrase Representations and Topic Mining](https://aclanthology.org/2022.acl-long.426.pdf) and [Personalized Showcases: Generating Multi-Modal Explanations for Recommendations](https://arxiv.org/pdf/2207.00422).

`meta-Hawaii.json` contains 21,507 rows and 15 columns recording the following information: 
- `name` - name of the business
- `address` - address of the business
- `gmap_id` - ID of the business
- `description` - description of the business
- `latitude` - latitude of the business
- `longitude` - longitude of the business
- `category` - category of the business
- `avg_rating` - average rating of the business
- `num_of_reviews` - number of reviews
- `price` - price of the business
- `hours` - open hours
- `MISC` - miscellaneous information
- `state` - current status of the business
- `relative_results` - related businesses recommended by Google
- `url` - URL of the business

### Data Cleaning and Exploratory Data Analysis
1. Keep only relevant columns from the metadata
    - We only keep the columns relevant to the research question, which include `name`, `category`, `longitude`, `price`, `avg_rating`, and `num_of_reviews`.
2. Remove rows missing necessary information
    - Rows missing `name`, `category`, or `avg_rating` were removed because these columns are necessary for identifying restaurants and analyzing their naming features and ratings.
3. Convert `price` into numeric price levels
    - The original `price` column uses dollar signs such as `$`, `$$`, `$$$`, and `$$$$`. These were converted to numeric levels 1 through 4 so price can be more easily compared and analyzed. Missing prices were kept as `NaN`. 
4. Filter the dataset to restaurants only
    - Since we are only analyzing the restaurant business, we only keep rows where at least one category contains the word `restaurant`. 
5. Clean and split restaurant names into individual words
    - Restaurant names were converted to lowercase and cleaned by removing punctuation and special characters. A new column, `name_words`, was then created to store the individual words in each restaurant name.
6. Create groups of naming features
    - Words were grouped into categories incuding food, cuisine, dining type, Hawaiian words, descriptive words, and person names. These groups make it easier to compare variables across broader naming patterns rather than individual words.
7. Add boolean columns for each naming feature
    - The columns `has_food_word`, `has_cuisine_word`. `has_dining_type_word`, `has_hawaiian_word`, `has_descriptive_word`, and `has_person_name` were added to indicate whether a restaurant name contains a word from each category. 

The cleaned dataset contains 4,301 rows and 14 columns. The following is the output of of the first 5 rows:

| name | category | longitude | price | avg_rating | num_of_reviews | name_words | has_food_word | has_cuisine_word | has_dining_type_word | has_hawaiian_word | has_descriptive_word | has_person_name |
|---|---|---:|---:|---:|---:|---|---|---|---|---|---|---|
| Hale Pops | [Restaurant] | -157.920714 | NaN | 4.4 | 18 | [hale, pops] | False | False | False | True | False | False |
| Akasatana Ramen Kyoto | [Ramen restaurant] | -157.843730 | NaN | 5.0 | 1 | [akasatana, ramen, kyoto] | True | False | False | False | False | False |
| Grill City | [Restaurant] | -158.026218 | NaN | 3.5 | 8 | [grill, city] | False | False | True | False | False | False |
| Buona Sera | [Italian restaurant, Restaurant] | -157.742702 | NaN | 3.7 | 24 | [buona, sera] | False | False | False | False | False | False |
| Tucker & Bevvy Breakfast | [Fast food restaurant, Restaurant] | -157.822399 | NaN | 4.2 | 57 | [tucker, bevvy, breakfast] | False | False | False | False | False | False |

##### Univariate Analysis
The distribution of the most common words in restaurant names shows that terms such as "restaurant", "grill", and "cafe" appear most frequently and can all be categorized as dining types. Many other common words describe foods or cuisines, giving insight into a few of the naming features that restaurants most commonly use.

<iframe
  src="assets/univariate_most_common_words.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

The distribution of word types in restaurant names shows `has_food_word` and `has_dining_type_word` are the  most common naming features. However, it is important to note that because these categories are manually defined, this plot should be interpreted broadly and only as a general classification of naming patterns rather than a precise or exhaustive measure.

<iframe
  src="assets/univariate_distribution_of_word_types.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

##### Bivariate Analysis
From this scatter plot, it can be interpreted that most restaurants have average ratings between approximately 3.5 and 5. Restaurants with fewer reviews show a wider range of ratings, while restaurants with more reviews tend to have ratings concentrated toward the higher end.

<iframe
  src="assets/biivariate_num_reviews_avg_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

The box plot shows that as price level increases, average rating also increases. Higher-priced restaurants appear to have more concentrated ratings than lower-priced restaurants, although it should be considered that there are fewer restaurants at the higher price levels.

<iframe
  src="assets/biivariate_price_level_avg_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

##### Aggregates
This pivot table aggreates the data by naming feature and displays the mean `avg_rating` and `price`. While the "Hawaiian" word type corresponds with the highest average rating, "Dining Type" and "Hawaiian" names have the highest average prices. However, the difference between the naming features are relatively small.

| word_type | avg_rating | price |
|---|---:|---:|
| Cuisine | 4.188274 | 1.599190 |
| Descriptive | 4.290206 | 1.627660 |
| Dining Type | 4.286122 | 1.853107 |
| Food | 4.222242 | 1.509774 |
| Hawaiian | 4.374708 | 1.781513 |
| Person Name | 4.118033 | 1.406250 |

### Assessment of Missingness

##### MNAR Analysis
I believe the `description` column could be argued as *MNAR* because whether a business has a description may depend on factors that are not observed in the dataset, such as how actively the owner manages the Google Maps listing. Additional data on business owner activity or the completeness of other fields in the listing, for example, could pottentially make the missingness *MAR* since it could then be explained by another observed variable.

##### Missing Dependency
> Missing Price Dependent on Number of Reviews
The missingness of `price` was tested to determine whether it depends on `num_of_reviews`.

**Null Hypothesis:** The missingness of `price` does not depend on `num_of_reviews`

**Alternative Hypothesis:** The missingness of `price` does depend on `num_of_reviews`

**Test Statistic:** The absolute difference in mean number of reviews between restaurants with missing prices and restaurants with non-missing prices.

**Significance Level:**: 0.05

<iframe
  src="assets/missingness_dependent_price.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

A permutation test was conducted by shuffling the `price_missing` labels 1000 times and recalculating the absolute difference in mean number of reviews of each permutation. The resulting p-value was less than 0.001.

Since the p-value is below the significance level of 0.05, there is sufficient evidence to reject the null hypothesis. This suggests that the missingness of `price` depends on `num_of_reviews`, and may therefore be considered *MAR*. This makes sense intuively because restaurants with more reviews may have more established and complete Google Maps listings, mking price information more likely to be recorded. Vise versa, restaurants with fewer reviews may have less complete listings, increasing the likelihood that `price` is missing.

> Missing Price Not Dependent on _________

### Hypothesis Testing

**Null Hypothesis:** Restaurants with Hawaiian words in their names have the same average price level as restaurants without Hawaiian words in their names.

**Alternative Hypothesis:** Restaurants with Hawaiian words in their names have a higher average price level than restaurants without Hawaiian words in their names.

**Test Statistic:** The absolute difference in mean number of reviews between restaurants with missing prices and restaurants with non-missing prices.

**Significance Level:**: 0.05