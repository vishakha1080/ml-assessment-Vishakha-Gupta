## B1. Problem Formulation
a)Machine Learning Formulation
The objective is to predict how many items will be sold under different promotional strategies.
Target variable: items_sold (number of items sold per store per month)
Input features: store characteristics (store size, location type, competition density), promotion type, time-based features (month, festival, weekend), and customer activity indicators (footfall, past sales patterns)
Type of ML problem: Supervised regression problem
Justification:
The target variable (items_sold) is continuous, so regression is appropriate. The model learns from past data where both inputs and outputs are known, making it a supervised learning task.
b) Why items_sold is better than revenue
Using items_sold is more reliable than total revenue because revenue can be influenced by pricing changes, discounts, or premium product sales, which may not reflect actual customer demand.
For example, a high discount may increase items sold but reduce revenue, or expensive items may inflate revenue without high volume.
This illustrates an important principle in machine learning:
The target variable should directly align with the business objective.
In this case, the goal is to maximize sales volume, not just revenue, so items_sold is a better measure of promotion effectiveness.
c) Alternative to a single global model
Instead of using one global model, a better approach is:
Segmented or hierarchical modeling
For example:
Build separate models for urban, semi-urban, and rural stores, OR
Include store-specific features or interaction terms in the model
Justification:
Different stores respond differently to promotions due to location, customer behavior, and competition. A single model may average out these differences and reduce accuracy. Segmenting allows the model to capture these variations more effectively.
## B2. Data and EDA Strategy
a) Data Joining and Dataset Design
The four tables would be joined using common keys:
transactions + store attributes → join on store_id
transactions + promotion details → join on promotion_id or date
transactions + calendar → join on transaction_date
Final dataset grain:
One row = one store per month
Aggregations:
Total items sold per store per month
Average or total footfall
Promotion applied in that month
Flags for weekend/festival presence
Store-level static features (size, location, competition)
This ensures the dataset is aligned with the prediction goal (monthly store-level sales).
b) EDA Strategy
Before modeling, the following analyses would be performed:
1. Promotion vs Sales Plot
Compare average items sold across different promotions
Helps identify which promotions perform better
May guide feature importance or encoding
2. Time Series Trend
Plot sales over months/years
Identify seasonality (e.g., festivals, year-end spikes)
Helps in creating time-based features
3. Store Type Comparison
Compare sales across urban, semi-urban, rural stores
Helps decide whether segmentation is needed
4. Correlation Analysis
Check relationship between numerical features (footfall, competition, sales)
Helps in feature selection and avoiding redundant variables
5. Distribution of Target Variable
Check if items_sold is skewed
Helps decide if transformation is needed
c) Handling Promotion Imbalance
If 80% of data has no promotion:
Impact:
Model may become biased toward predicting outcomes without promotions
It may underestimate the effect of promotions
Solutions:
Use balanced sampling (oversample promotion cases)
Add promotion as an important feature
Evaluate model performance separately for promoted vs non-promoted cases
## B3. Model Evaluation and Deployment
a) Train-Test Split and Metrics
Split strategy:
Use time-based split
Train on earlier months, test on recent months
Why not random split?
Random split mixes past and future data
Causes data leakage and unrealistic performance
Metrics:
RMSE (Root Mean Squared Error):
Measures overall prediction error, penalizes large mistakes more
MAE (Mean Absolute Error):
Measures average error, easier to interpret
Interpretation:
Lower values mean better predictions. These metrics show how close predicted sales are to actual sales.
b) Explaining Model Decisions (Feature Importance)
To understand why the model gives different recommendations:
Check feature importance from the model
Analyze how features like:
month (seasonality)
festival presence
past sales patterns
affect predictions
For example:
December may favor loyalty bonuses due to festive shopping
March may respond better to discounts due to lower demand
Communication:
Explain to stakeholders that:
The model adapts recommendations based on seasonal patterns and customer behavior, not just store identity.
c) Deployment Process
1. Save the model
Use tools like joblib or pickle to save the trained pipeline
2. Prepare new data
At the start of each month:
Collect latest store data, promotions, and calendar features
Apply same preprocessing steps
3. Generate predictions
Load saved model
Predict items_sold for each promotion option
Recommend the best promotion per store
4. Monitoring
Track:
Prediction errors over time
Sudden drops in accuracy
Changes in data patterns (data drift)
5. Retraining
Retrain model when:
Performance drops significantly
New data patterns emerge
