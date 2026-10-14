**Project status:** On going...

# 🎯Problem statement

Rossmann operates over 3,000 drug stores in 7 European countries. Store sales are influenced by many factors, including promotions, competition, school and state holidays, seasonality, and locality. We are provided with historical sales data for 1,115 Rossmann stores in Germany.

**Goal:** Predict 6 weeks of daily sales for 1,115 stores located across Germany.

**How will this help business:** Reliable sales forecasts enable store managers to create effective staff schedules that increase productivity and motivation. By helping Rossmann create a robust prediction model, we will help store managers stay focused on what’s most important to them: their customers and their teams!

**Methods:** Random Forest and XGBoost


**Evaluation Metrics:** MAE and R-Squared



## 📦Dataset
[Rossmann Store Sales](https://www.kaggle.com/competitions/rossmann-store-sales) dataset on Kaggle - A competition dataset with more than 1M data points for **Training** including some additional information about each stores.


## 🧹Cleaning & 🔍EDA

* Replaced missing `CompetitionDistance` with its max value and `Promo2SinceWeek`, `Promo2SinceYear`, `PromoInterval` `CompetitionOpenSinceMonth`, `CompetitionOpenSinceYear` with 0s

* Extracted `Year`, `Month`, & `Day` from `Date` column.

* Dropped rows where closed stores had zero sales 

* There are 3593 Sunday when the store was open

* In lifetime (2 years), `StoreType a` has the most Customers and most Sales.

* There are least `StoreType b` stores(15,560) in the dataset and yet it is the most crowded with highest median sales on daily basis in every Month, with or without promotion (Promo), because it has the most(8209) stores with **Extra / Standard Assortment**, then **6409** stores with **Basic Assortment** and lastly **942** flagship or high-volume stores with **Extended / Advanced Assortment**.

* Saved the clean `merged_df` as `merged.csv` to work with it in another notebook.


## ✂️Train-Test Split

* Kept last 6 weeks of data(40,282) to test and the rest(8,04,056) for training


## ⚙️Preprocessing

* Before preprocessing only relevant columns have been chosen as input columns and **`Sales`** as output column.

* Encoded categorical columns using `OneHotEncoder`.

* Scaled numerical columns using `StandardScaler`.


## 🌲Baseline Random Forest:

* On the test set, the baseline model is off by about **€789.43** (in either direction) which means MAE is `11%` of the **average daily sales**.

* This baseline model explains 83.67% of the variance in daily sales.

* **Feature Importance:** The baseline random forest picked **`CompetitionDistance`** as the most important feature.


## 🚀Baseline XGBoost:

* On the test set, the baseline XGBoost is off by about **€1104.47** which means MAE is `15.83%` of the **average daily sales**. 

* It explains `73%` of the variance in daily sales.

* **Feature Importance:** The baseline xgboost picked `Assortment_b` as the most importnat feature.


## Random Forest Hyperparameter Tuning:

| Model | MAE | R² |
|---|---|---|
| Baseline (default hyperparameters) | €789 | 0.837 |
| Tuned (9 combinations, 5-fold TimeSeriesSplit) | €776 | 0.839 |

**Conclusion:** MAE fell by about €13 (roughly 1.6%) and R² rose by 0.002.

**Limitations:**
- Only 9 combinations were tested, so a better setting may exist.
- I did not change one hyperparameter at a time, so I can't say which setting produced the gain.



## XGBoost Hyperparameter Tuning:
Tuned XGBoost over 8 rounds of randomized search (TimeSeriesSplit cross-validation) and compared it with the untuned baseline and with the Random Forest from earlier. All models use the same features, training data and test set.

| Model | Test MAE | Test R² |
|---|---|---|
| XGBoost baseline (defaults) | €1,104 | 0.73 |
| XGBoost, round 3 (worst) | €1,726 | 0.40 |
| XGBoost, round 5 | €959 | 0.784 |
| XGBoost, round 7 | €806 | 0.831 |
| **XGBoost, round 8 (best)** | **€779** | **0.836** |
