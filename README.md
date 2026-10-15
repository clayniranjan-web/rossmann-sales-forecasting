
## 📈Rossmann Sales Forecasting: Random Forest vs XGBoost

Rossmann is a drugstore chain in Europe. Store managers need to know how much each store will sell, so they can plan how many staff to have on each day. Too few staff on a busy day means poor service. Too many staff on a quiet day wastes money.

This project forecasts **daily sales for each of 1,115 Rossmann stores in Germany, 6 weeks ahead**, using historical store-level sales together with promotion, holiday, and competition information. It then compares two tree-based models, Random Forest and XGBoost, to see which forecasts better and whether tuning is worth the effort.

### Data

- Source: Kaggle's [Rossmann Store Sales](https://www.kaggle.com/competitions/rossmann-store-sales) competition (`train.csv` and `store.csv`, merged on `Store`)
- Period: 01-January-2013 to 31-July-2015
- Target: `Sales`

### Validation setup

The data is time-ordered, so the split is by date, not random. The **last 6 weeks** are held out as the test set and are scored once, at the end. Hyperparameters are tuned using `TimeSeriesSplit` inside the training period only, so no future information reaches training.

### Evaluation metrics

- **MAE:** the typical miss in sales units, which is easy to interpret
- **R²:** the share of variance in daily sales the model explains

### Methods

Random Forest and XGBoost, each evaluated untuned and tuned.



## 🧹Cleaning & 🔍EDA

* Replaced missing `CompetitionDistance` with its max value and `Promo2SinceWeek`, `Promo2SinceYear`, `PromoInterval` `CompetitionOpenSinceMonth`, `CompetitionOpenSinceYear` with 0s

* Extracted `Year`, `Month`, & `Day` from `Date` column.

* Dropped rows where closed stores had zero sales 

* There are 3593 Sunday when the store was open

* In lifetime, `StoreType a` has the most Customers and most Sales.

* There are least `StoreType b` stores(15,560) in the dataset and yet it is the most crowded with highest median sales on daily basis in every Month, with or without promotion (Promo), because it has the most(8209) stores with **Extra / Standard Assortment**, then **6409** stores with **Basic Assortment** and lastly **942** flagship or high-volume stores with **Extended / Advanced Assortment**.

* Saved the clean `merged_df` as `merged.csv` to work with it in another notebook.


## ✂️Train-Test Split

* Kept last 6 weeks of data(40,282) to test and the rest(8,04,056) for training


## ⚙️Preprocessing

* Before preprocessing only relevant columns have been chosen as input columns and **`Sales`** as output column.

* Encoded categorical columns using `OneHotEncoder`.

* Scaled numerical columns using `StandardScaler`.



## 🔍 Methodology & Findings

### Untuned Random Forest:

* On the test set, the untuned model is off by about **€789.43** (in either direction) which means MAE is `11%` of the **average daily sales**.

* This untuned model explains 83.67% of the variance in daily sales.

* **Feature Importance:** The untuned random forest picked **`CompetitionDistance`** as the most important feature.


### Untuned XGBoost:

* On the test set, the untuned XGBoost is off by about **€1104.47** which means MAE is `15.83%` of the **average daily sales**. 

* It explains `73%` of the variance in daily sales.

* **Feature Importance:** The untuned xgboost picked `StoreType_b` as the most importnat feature.


## Random Forest Hyperparameter Tuning:

| Model | MAE | R² |
|---|---|---|
| Untuned (default hyperparameters) | €789 | 0.837 |
| Tuned (9 combinations, 5-fold TimeSeriesSplit) | €776 | 0.839 |

*NOTE: The randomized searches ran on a 400,000-row subsample of the training set because of memory limits.*

**Result:** MAE fell by about €13 (roughly 1.6%) and R² rose by 0.002.

**Limitations:**
- Only 9 combinations were tested, so a better setting may exist.
- I did not change one hyperparameter at a time, so I can't say which setting produced the gain.

**Feature Importance on Tuned Random Forest:**\
Just like Untuned Random Forest, Tuned Random Forest as well picked `CompetitionDistance` as the most important and `Promo` as the 2nd most important feature.


## XGBoost Hyperparameter Tuning:

Tuned XGBoost over 8 rounds of randomized search (TimeSeriesSplit cross-validation) and compared it with the untuned XGBoost and with the Random Forest from earlier. All models use the same features, training data and test set.

| Model | MAE | R² |
|---|---|---|
| XGBoost untuned (defaults) | €1,104 | 0.73 |
| XGBoost, round 3 (worst) | €1,726 | 0.40 |
| XGBoost, round 5 | €959 | 0.784 |
| XGBoost, round 7 | €806 | 0.831 |
| **XGBoost, round 8 (best)** | **€779** | **0.836** |

*NOTE: The randomized searches ran on a 400,000-row subsample of the training set because of memory limits.*

**Result:** tuning cut XGBoost's test MAE from €1,104 to €779, about 30%. R² rose from 0.73 to 0.836.

**Feature Importance on Tuned Random Forest:**\
just like the Untuned XGBoost, Tuned XGBoost as well picked `StoreType_b` as the first most important and `Promo` as the 2nd most important feature.



## Conclusion

All models use the same features, the same training data, and the same test set (the last 6 weeks).

| Model | Test MAE | Test R² |
|---|---|---|
| Random Forest, untuned | 789 | 0.837 |
| Random Forest, tuned | 776 | 0.839 |
| XGBoost, untuned | 1,104 | 0.73 |
| XGBoost, tuned | 779 | 0.836 |

**Which model is better?** After tuning, Random Forest and XGBoost perform almost the same (MAE 776 vs 779). A difference of 3 is too small to call a winner, so on this data neither model is clearly better.

**Is tuning worth it?**
- For XGBoost, yes. The default settings were poor, and tuning cut MAE by about 30% (1,104 to 779).
- For Random Forest, barely. Tuning improved MAE by about 13 (1.6%), which may be within the noise of this setup.

**Feature importance:** `CompetitionDistance` (Random Forest) and `StoreType_b` (XGBoost) ranked highest. These values are fixed for each store, so they may be standing in for store identity, and this does not show that they cause higher sales.

**Limitations**
- Tuning used a 400,000-row subsample because of memory limits, and only [9 / 8] parameter combinations were tried, so better settings may exist.
- The test set is a single 6-week window. It contains no December, which was the biggest sales spike in the EDA, so results for holiday periods are untested.
- Errors are reported as one overall number. I did not break them down by store type, promotion status, or store, so I can't say where the models do worst.



## 🗂️ Repo Structure

```
01_eda_cleaning.ipynb      # Data loading, Cleaning, Time-series based feature engineering
02_modeling.ipynb          # Basline Random Forest, Baseline XGBoost, Hyperparameter tuning
```

> `data/` is gitignored and not part of this repo — see **How to Reproduce** below.

## ⚙️ How to Reproduce

1. Download the dataset from the [Kaggle link](https://www.kaggle.com/competitions/rossmann-store-sales) above and place it under `data/`
2. Run `notebooks/01_eda_cleaning.ipynb` — outputs `merged.csv`
3. Run `notebooks/02_modeling.ipynb`
