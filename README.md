**Project status:** On going...

# 🎯 Problem statement

Rossmann operates over 3,000 drug stores in 7 European countries. Store sales are influenced by many factors, including promotions, competition, school and state holidays, seasonality, and locality. We are provided with historical sales data for 1,115 Rossmann stores in Germany.

**Goal:** Predict 6 weeks of daily sales for 1,115 stores located across Germany.

**How will this help business:** Reliable sales forecasts enable store managers to create effective staff schedules that increase productivity and motivation. By helping Rossmann create a robust prediction model, we will help store managers stay focused on what’s most important to them: their customers and their teams!

**Methods:** Random Forest and XGBoost

**Evaluation:** Root Mean Square Percentage Error (RMSPE)

## 📦 Dataset
[Rossmann Store Sales](https://www.kaggle.com/competitions/rossmann-store-sales) dataset on Kaggle - A competition dataset with four files: `train.csv`, `test.csv`, `store.csv`, `sample_submission.csv`; more than 1M data points for training.


## 🧭 Approach

| Stage | What happened | 
|---|---|
| **Missing Values** | Replaced missing `CompetitionDistance` with its max value and `Promo2SinceWeek`, `Promo2SinceYear`, `PromoInterval`, `CompetitionOpenSinceMonth`, `CompetitionOpenSinceYear` with 0s |