# B1. Problem Formulation

 ## (a) 

ML Problem Definition 
Target Variable:
items_sold (number of items sold per store per month)

Candidate Input Features:
Store attributes: store_id, store_size, location_type
Promotion details: promotion_type
Calendar features: is_weekend, is_festival, month, year
Market factors: competition_density

Type of ML Problem:
Supervised Regression Problem

Justification:
The goal is to predict a continuous numeric value (items_sold) based on historical data. Since the output is quantitative and not categorical, regression is the appropriate choice.

## (b) 

Why Use Items Sold Instead of Revenue 
Reasons:
Revenue depends on price variations, discounts, and product mix.
Promotions directly influence volume (demand) rather than price.
Revenue may be artificially inflated by higher-priced items, masking true promotion effectiveness.
Items sold isolates the pure promotion impact on customer behavior.

Broader Principle:

The target variable should directly measure the business objective being optimized.
In this case, the objective is to maximize demand, not revenue distortions due to pricing strategies.

## (c) 

 Alternative Modelling Strategy 
Instead of a single global model:
Proposed Approach:
  Segmented / Hierarchical Models
Separate models for:
  Urban
  Semi-urban
  Rural
  OR
  Include interaction features (e.g., promotion_type × location_type)

Justification:
Customer behavior and response to promotions differ by location.
A global model may average out important patterns, reducing accuracy.
Segmented models capture local behavior more effectively.



# B2. Data and EDA Strategy

## (a) 

Data Joining and Dataset Design (4 marks)
Tables:
  transactions
  store_attributes
  promotion_details
  calendar

Joins:
Join on:
  store_id → store attributes
  promotion_type → promotion details
  transaction_date → calendar

Final Dataset Grain:
  One row = one store per month
  Aggregations:
  
  items_sold → total per store per month
  transactions → count (optional)
  Average competition / flags retained

  ## (b) 
  
  EDA Strategy 
  
Promotion vs Items Sold (Box Plot)
  Identify which promotions drive higher sales
  Helps prioritize impactful promotions


Time Series Trend
  Items sold over months
  Detect seasonality (e.g., holiday spikes)


Location-based Analysis (Bar Chart)
  Compare urban vs rural performance
  Helps decide segmentation strategy


Correlation Heatmap

  Identify relationships between numerical features
  Detect multicollinearity or weak signals

Impact on Modeling:

  Drives feature selection
  Identifies need for interaction features
  Influences model choice (linear vs nonlinear)

## (c) 

Promotion Imbalance Issue 

Problem:

80% data has no promotion → model biased toward non-promo cases
Harder to learn promotion impact

Mitigation:

Use balanced sampling or weighted models
Create binary feature: promotion_applied
Ensure sufficient representation of all promotion types


# B3. Model Evaluation and Deployment

## (a) 

Train-Test Split & Metrics (4 marks)


Split Strategy:

Use time-based split
Train: first ~2.5 years
Test: last ~6 months



Why Not Random Split:

Causes data leakage (future influencing past)
Unrealistic evaluation



Evaluation Metrics:

| Metric        | Purpose                    | Interpretation                          |
|---------------|----------------------------|----------------------------------------|
| RMSE          | Penalizes large errors     | Sensitive to big prediction mistakes   |
| MAE           | Average absolute error     | Easier business interpretation         |
| R² (optional) | Variance explained         | Overall model fit                      |


## (b) 

Explaining Different Recommendations 

Use feature importance:
  Check top drivers:

    Month (seasonality)
    Festival flag
    Promotion type interactions
    Location trends

Example Explanation:
  December:
  High demand season → Loyalty Points maximizes repeat/customer retention

  March:
  Lower baseline demand → Flat Discount boosts conversions

  Communication Approach:

  Use simple visuals (feature importance charts)
  Explain in business terms (seasonality, demand gaps)

## (c) 

Deployment Strategy 

1. Model Saving
    Python
       import joblib
       joblib.dump(model, "promotion_model.pkl")
   
2. Monthly Inference Pipeline
    Collect new data:
      Store attributes
      Calendar features
    Apply same preprocessing pipeline
    Generate predictions for each promotion
    Select promotion with highest predicted items_sold

3. Automation Flow

  Scheduled job (monthly)
  Input → preprocess → predict → output recommendations

4. Monitoring & Retraining
Track:
  Prediction vs actual error (RMSE, MAE)
  Data drift (feature distributions changing)
  Business KPIs (sales drop)

Triggers for retraining:
  Significant increase in error
  Seasonal pattern change
  New promotion types introduced




















