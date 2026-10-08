**Project status:** On going...

# 🎯Problem statement

Rossmann operates over 3,000 drug stores in 7 European countries. Store sales are influenced by many factors, including promotions, competition, school and state holidays, seasonality, and locality. We are provided with historical sales data for 1,115 Rossmann stores in Germany.

**Goal:** Predict 6 weeks of daily sales for 1,115 stores located across Germany.

**How will this help business:** Reliable sales forecasts enable store managers to create effective staff schedules that increase productivity and motivation. By helping Rossmann create a robust prediction model, we will help store managers stay focused on what’s most important to them: their customers and their teams!

**Methods:** Random Forest and XGBoost

**Evaluation:** Root Mean Square Percentage Error (RMSPE)

## 📦Dataset
[Rossmann Store Sales](https://www.kaggle.com/competitions/rossmann-store-sales) dataset on Kaggle - A competition dataset with more than 1M data points for **Training** and 41k+ for **Testing**.


## 🧹Cleaning & 🔍EDA

* Replaced missing `CompetitionDistance` with its max value and `Promo2SinceWeek`, `Promo2SinceYear`, `PromoInterval` `CompetitionOpenSinceMonth`, `CompetitionOpenSinceYear` with 0s

* Extracted `Year`, `Month`, & `Day` from `Date` column.

* Dropped rows where closed stores had zero sales 

* There are 3593 Sunday when the store was open

* In lifetime (2 years), `StoreType a` has the most Customers and most Sales.

* There are least `StoreType b` stores(15,560) in the dataset and yet it is the most crowded with highest median sales on daily basis in every Month, with or without promotion (Promo), because it has the most(8209) stores with **Extra / Standard Assortment**, then **6409** stores with **Basic Assortment** and lastly **942** flagship or high-volume stores with **Extended / Advanced Assortment**.

