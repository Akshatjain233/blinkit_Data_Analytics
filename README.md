# 🛒 BlinkIT Retail Sales & Outlet Performance Analysis

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-v2.0%2B-darkblue?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-v1.2%2B-orange?logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![Power BI](https://img.shields.io/badge/Power_BI-Dashboard-yellow?logo=power-bi&logoColor=black)](https://powerbi.microsoft.com/)
[![Tableau](https://img.shields.io/badge/Tableau-Visualization-red?logo=tableau&logoColor=white)](https://public.tableau.com/)

An end-to-end data analytics and predictive modeling project analyzing **BlinkIT's retail sales data** (8,523 rows × 12 columns). This project implements a complete data science pipeline: from raw data cleaning and feature engineering to statistical hypothesis testing, machine learning forecasting, and interactive dashboard design in both Power BI and Tableau.

---

## 📌 Executive Summary & Key Achievements
* **Exploratory Data Analysis**: Preprocessed 8,523 entries, resolving high-volume missing data (~28% missing in `Outlet_Size`, ~17% in `Item_Weight`) using group-level medians and modes.
* **Feature Engineering**: Engineered 5 key domain-specific features including **Outlet Age**, **Sales per Visibility**, **Price Tiers (MRP Tiers)**, and **Broad Item Categories**.
* **Statistical Validation**:
  * Confirmed that **Tier 2 and Tier 3 outlets differ significantly** in average sales via a two-sample independent t-test ($p < 0.05$).
  * Conducted a **Pareto Analysis (80/20 Rule)** to discover that the top 5 of 16 item types drive over 50% of the total revenue.
* **Predictive Modeling**: Trained and evaluated **Linear Regression** and **Random Forest Regressor** models to forecast item outlet sales, with the Random Forest model achieving an $R^2 \approx 0.55$.
* **Interactive Dashboards**: Designed comprehensive BI dashboards mapping KPIs, sales trends, outlet age performance, and placement effectiveness (identifying "Hidden Gems" vs. "Ineffective Placements").

---

## 📁 Repository Structure

```
Retail-sales-blinkit/
├── data/
│   └── Tableau BlinkIT Grocery Project...xlsx  # Raw retail sales dataset
├── notebooks/
│   ├── 01_eda_cleaning.ipynb                   # Data cleaning and EDA
│   ├── 02_feature_engineering.ipynb            # Variable creation & binning
│   ├── 03_statistical_tests.ipynb              # T-tests & Pareto analysis
│   └── 04_ml_models.ipynb                      # Regression & Random Forest
├── outputs/                                    # Generated CSVs and figures
│   ├── cleaned_data.csv
│   ├── featured_data.csv
│   ├── model_results.csv
│   ├── statistical_results.csv
│   ├── actual_vs_predicted.png
│   ├── feature_importance.png
│   ├── pareto_chart.png
│   └── ... (additional visualizations)
├── blinkit_project_guide.md                    # Python & Power BI guide
├── blinkit_tableau_guide.md                    # Tableau visual recreation guide
└── README.md                                   # Project documentation (this file)
```

---

## ⚙️ Project Pipeline

### Phase 1: Exploratory Data Analysis & Cleaning
* **Missing Value Imputation**:
  * `Item_Weight` filled using the median weight per specific `Item_Type` to ensure robustness against outliers.
  * `Outlet_Size` imputed using the mode (most common size) grouped by `Outlet_Type`.
* **Label Harmonization**: Corrected inconsistent data entry values for `Item_Fat_Content` (e.g., standardizing `LF`, `low fat` $\rightarrow$ `Low Fat`, and `reg` $\rightarrow$ `Regular`).
* **Visualizations**: Generated distribution plots of target sales and box plots across different outlet types.

### Phase 2: Feature Engineering
Created new parameters to reveal deeper insights for modeling:
1. **Outlet Age**: Derived from the establishment year ($2024 - \text{Establishment Year}$).
2. **Sales per Visibility**: Measures revenue generated per unit of product visibility ($\frac{\text{Sales}}{\text{Visibility}}$).
3. **MRP Tier**: Grouped maximum retail prices into four logical bins (`Low`, `Medium`, `High`, `Very High`).
4. **Is New Outlet**: Binary flag separating stores established post-2000 from legacy outlets.
5. **Item Category**: Aggregated 16 detailed item types into 4 macro-categories (`Food`, `Drinks`, `Snacks`, `Non-Food`).

### Phase 3: Statistical Hypothesis Testing
* **T-Test (Tier 2 vs. Tier 3 Locations)**:
  * **Hypothesis**: Tier 2 and Tier 3 outlets perform competitively identical.
  * **Result**: Rejected the null hypothesis ($t \text{-statistic} = -4.95$, $p < 0.05$). Tier 3 locations generate statistically higher sales than Tier 2.
* **Pareto Analysis**: Evaluated sales concentration by item type. Found that Snacking, Fruits & Vegetables, Household, Frozen Foods, and Dairy account for the majority of sales volume.

### Phase 4: Predictive Machine Learning Models
Predicting the target variable `Item_Outlet_Sales` using regression models:
* **Preprocessing**: Applied Label Encoding to categorical fields. Split data using an 80/20 train/test ratio.
* **Models Trained**:
  1. **Linear Regression** (Baseline)
  2. **Random Forest Regressor** (Max depth = 10, Estimators = 100)

#### Model Performance Comparison:
| Model | RMSE | MAE | $R^2$ Score |
|---|---|---|---|
| **Linear Regression** | 1205.15 | 895.53 | 0.505 |
| **Random Forest** | 1083.84 | 741.05 | 0.601 |

* **Feature Importance**: Random Forest identified **Item MRP** as the single most critical driver of sales performance (~45.3% importance weight), followed by **Outlet Type** (~23.4%).

---

## 📊 Business Intelligence Dashboards

### Power BI Dashboard
Includes 4 key cards (Total Sales, Average Sales, Average Rating, Item Count) and interactive visuals:
* **Sales by Outlet Type** (Clustered Bar Chart)
* **Sales by Item Type** (Donut Chart)
* **Fat Content Share** (Pie Chart)
* **Sales Trends by Outlet Age** (Line Chart)
* **Sales by Outlet Size and Tier** (Grouped Bars/Slicers)

### Tableau Dashboard
Recreates detailed page layouts to perform deep visual analytics:
* **Hidden Gems vs. Ineffective Placements**: A custom quadrant scatter plot highlighting items with low visibility but high sales (potential scale-up opportunities) versus high visibility but low sales.
* **Weighted Sales share**: Evaluates sales distribution adjusted by the product price levels.
* **Location Tier breakdown**: Maps total sales, average sales per item, and average MRP side-by-side.

---

## 🚀 Getting Started

### Prerequisites
Install Python 3.10+ and the required packages:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy openpyxl
```

### Running the Python Pipeline
1. Clone the repository and navigate to the directory:
   ```bash
   git clone https://github.com/Akshatjain233/blinkit_Data_Analytics.git
   cd blinkit_Data_Analytics
   ```
2. Run notebooks in sequence:
   * `01_eda_cleaning.ipynb`
   * `02_feature_engineering.ipynb`
   * `03_statistical_tests.ipynb`
   * `04_ml_models.ipynb`

---

## 💡 Resume Bullet Points
If you are featuring this project on your resume, you can highlight it as:
> **Retail Sales & Outlet Performance Analysis (BlinkIT)** | *Python, Power BI, Tableau, Machine Learning*
> * Conducted EDA on a retail dataset of 8,523 records using Pandas and NumPy; handled missing data with group-based median/mode imputation and engineered key features (Outlet Age, Price Tiers, Sales per Visibility).
> * Validated Pareto sales concentration and Tier 2/3 outlet competitiveness via independent-sample t-tests ($p < 0.05$).
> * Trained Linear Regression and Random Forest models to predict outlet sales, achieving an $R^2 \approx 0.60$; built interactive Power BI and Tableau dashboards visualizing sales trends, efficiency KPIs, and placement optimization quadrant metrics.
