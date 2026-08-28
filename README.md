# hawaii-restaurant-analysis
DSC 80 project analyzing Hawaii restaurant pricing, ratings, and naming patterns.

## Introduction
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

## Data Cleaning and Exploratory Data Analysis
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