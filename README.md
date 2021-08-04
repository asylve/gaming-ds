# Project Summary

The goal of this project is to predict which game users that are likely to spend money after finishing a game's tutorial. If such users can be identified based on their early behavior (before they spend anything), they can be offered different pricing to try and improve overall profits.
The provided data is split into three tables:
-	ka_actions – Describing actions the user has taken while playing the game
-	ka_devices – Describing the mobile device the user is playing the game on
-	ka_users – Describing other miscellaneous information about the user

The ‘ka_users’ table contains a column 'total_spend' indicating the amount of money the player has spent in the game to date (assumed). For all other columns, it is assumed that the data would be available for a new user on which we would like to run the prediction algorithm.

In the attached workflow, a logistic regression model is developed which classifies users based on whether they will spend money ('total spend' !=0) or will not spend money ('total_spend' == 0). The steps of the work are:
-	Load data:
    -	Load data from the three separate sources provided (csv, Apache Parquet, and SQLite) into a pandas DataFrames
    -	Combine all DataFrames into one (each DataFrame has a unique row for each user)
-	Investigate and remove missing values
-	Investigate correlations between variables
-	Create a target variable indicating if the user spent money in the game or not
-	Prepare features for model training:
    -	Identify numeric columns, remove skewness, and standardize values
    -	Identify low-cardinality categorical columns for one-hot encoding
    -	Identify high-cardinality categorical columns for target encoding and aggregate values with a low number of occurrences into a single ‘other’ variable
-	Create a training pipeline and train a logistic regression model to predict which users will spend money in the game (80/20 train/test split)
-	Plot precision, recall, and ROC and select an appropriate classification threshold

The trained model's AUC is 0.85 indicating it can predict users who will spend money significantly more often than random guessing. Due to the strong class imbalance in the dataset (more than 98% of users never spend money), it was important to select an appropriate classification threshold when making predictions with this model. With the chosen threshold, the model had a 75.0% recall, 5.96% precision, and 21.8% false positivity rate. 

It may be that users can broadly be grouped into two groups:
1.	Those who are likely to spend money and are price insensitive
2.	Those who are unlikely to spend money and are price sensitive

In this case, a good strategy might be to increase pricing on group 1 to capitalize on their behavior while decreasing the price on group 2 to try and persuade them to spend something. Using the model this way, we can expect:
-	75% of group 1 correctly receives higher pricing
-	25% of group 1 incorrectly receives lower pricing
-	72.2% of group 2 correctly receives lower pricing
-	21.8% of group 2 incorrectly receives higher pricing
