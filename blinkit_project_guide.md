# BlinkIT Retail Sales & Outlet Performance Analysis
## Complete Beginner's Step-by-Step Guide

---

## 📌 What You Will Build

By the end of this project, you will have:
- A **cleaned and feature-engineered dataset** (Python + Pandas)
- **Statistical analysis** (t-tests, Pareto analysis)
- **Two ML models** (Linear Regression + Random Forest) to predict item sales
- An **interactive Power BI dashboard** with KPIs

This project covers the exact bullet points on your resume:
> *"Conducted EDA on an 11-file, 75-column retail dataset... validated Pareto sales concentration and Tier 2/3 outlet competitiveness via t-tests... trained Linear Regression and Random Forest models... delivered an interactive Power BI dashboard."*

---

## 🧰 Prerequisites: What to Install First

### Step 1 — Install Python
1. Go to [https://www.python.org/downloads/](https://www.python.org/downloads/)
2. Download Python 3.10 or newer
3. During installation, **tick the box that says "Add Python to PATH"**
4. Open Command Prompt (Windows) or Terminal (Mac) and type:
   ```
   python --version
   ```
   You should see something like `Python 3.11.4`

### Step 2 — Install VS Code (Code Editor)
1. Download from [https://code.visualstudio.com/](https://code.visualstudio.com/)
2. Install the **Python extension** inside VS Code (Extensions tab → search "Python")

### Step 3 — Install Jupyter Notebook
Open your terminal and run:
```bash
pip install jupyter notebook
```
To start Jupyter: `jupyter notebook` — this opens a browser tab where you write code.

### Step 4 — Install Required Python Libraries
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy openpyxl
```

### Step 5 — Install Power BI Desktop (Free)
1. Go to [https://powerbi.microsoft.com/desktop/](https://powerbi.microsoft.com/desktop/)
2. Download and install Power BI Desktop (Windows only; Mac users can use a VM or skip to browser version)

---

## 📁 Project Folder Structure

Create a folder called `blinkit_project` on your Desktop. Inside it, create these subfolders:

```
blinkit_project/
├── data/
│   └── BlinkIT_Grocery_Data.xlsx     ← put your dataset here
├── notebooks/
│   └── 01_eda_cleaning.ipynb
│   └── 02_feature_engineering.ipynb
│   └── 03_statistical_tests.ipynb
│   └── 04_ml_models.ipynb
├── outputs/
│   └── cleaned_data.csv
│   └── model_results.csv
├── dashboard/
│   └── blinkit_dashboard.pbix
└── README.md
```

---

## 📊 Phase 1: Exploratory Data Analysis (EDA) & Data Cleaning

Open Jupyter Notebook and create `01_eda_cleaning.ipynb`.

### Understanding the Dataset
Your dataset has **8,523 rows** and **12 columns**:

| Column | Type | Meaning |
|---|---|---|
| Item_Identifier | Text | Unique product code (e.g. FDA15) |
| Item_Weight | Number | Weight of the product |
| Item_Fat_Content | Text | Low Fat / Regular |
| Item_Visibility | Number | % shelf space the item occupies |
| Item_Type | Text | Category (Dairy, Meat, Snack Foods, etc.) |
| Item_MRP | Number | Maximum Retail Price |
| Outlet_Identifier | Text | Store code (OUT049, etc.) |
| Outlet_Establishment_Year | Number | Year the store was opened |
| Outlet_Size | Text | Small / Medium / High |
| Outlet_Location_Type | Text | Tier 1 / Tier 2 / Tier 3 |
| Outlet_Type | Text | Grocery Store / Supermarket Type1/2/3 |
| Item_Outlet_Sales | Number | **Target variable — sales amount** |

### Step-by-Step Code

```python
# ============================================================
# NOTEBOOK 1: EDA & DATA CLEANING
# ============================================================

# --- Cell 1: Import libraries ---
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Set plot style
sns.set_style("whitegrid")
plt.rcParams['figure.figsize'] = (12, 6)

# --- Cell 2: Load the data ---
df = pd.read_excel('../data/BlinkIT_Grocery_Data.xlsx')
print("Shape:", df.shape)
print("\nFirst 5 rows:")
df.head()
```

```python
# --- Cell 3: Basic info ---
df.info()
print("\nNull values per column:")
print(df.isnull().sum())
```

You will see:
- `Item_Weight` has **1,463 missing values**
- `Outlet_Size` has **2,410 missing values**

```python
# --- Cell 4: Summary statistics ---
df.describe()
```

```python
# --- Cell 5: Fix inconsistent Fat Content labels ---
# 'Low Fat', 'low fat', 'LF' all mean the same thing
# 'Regular', 'reg' mean the same thing
df['Item_Fat_Content'] = df['Item_Fat_Content'].replace({
    'low fat': 'Low Fat',
    'LF': 'Low Fat',
    'reg': 'Regular'
})
print("Fat Content values after fix:")
print(df['Item_Fat_Content'].value_counts())
```

```python
# --- Cell 6: Fill missing Item_Weight with median per item type ---
# Why median? It is robust to outliers unlike mean.
df['Item_Weight'] = df.groupby('Item_Type')['Item_Weight'].transform(
    lambda x: x.fillna(x.median())
)
print("Missing Item_Weight after fill:", df['Item_Weight'].isnull().sum())
```

```python
# --- Cell 7: Fill missing Outlet_Size ---
# Fill with the mode (most common value) for each Outlet_Type
outlet_size_mode = df.groupby('Outlet_Type')['Outlet_Size'].agg(
    lambda x: x.mode()[0] if not x.mode().empty else 'Small'
)
df['Outlet_Size'] = df.apply(
    lambda row: outlet_size_mode[row['Outlet_Type']] 
    if pd.isnull(row['Outlet_Size']) else row['Outlet_Size'],
    axis=1
)
print("Missing Outlet_Size after fill:", df['Outlet_Size'].isnull().sum())
```

```python
# --- Cell 8: Distribution of target variable ---
plt.figure(figsize=(10, 5))
sns.histplot(df['Item_Outlet_Sales'], bins=50, kde=True, color='steelblue')
plt.title('Distribution of Item Outlet Sales')
plt.xlabel('Sales')
plt.ylabel('Count')
plt.tight_layout()
plt.savefig('../outputs/sales_distribution.png', dpi=150)
plt.show()
```

```python
# --- Cell 9: Sales by Outlet Type ---
plt.figure(figsize=(10, 5))
sns.boxplot(data=df, x='Outlet_Type', y='Item_Outlet_Sales', palette='Set2')
plt.title('Sales Distribution by Outlet Type')
plt.xticks(rotation=15)
plt.tight_layout()
plt.savefig('../outputs/sales_by_outlet_type.png', dpi=150)
plt.show()
```

```python
# --- Cell 10: Sales by Item Type ---
item_sales = df.groupby('Item_Type')['Item_Outlet_Sales'].mean().sort_values(ascending=False)
plt.figure(figsize=(12, 6))
item_sales.plot(kind='bar', color='coral')
plt.title('Average Sales by Item Type')
plt.ylabel('Average Sales')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.savefig('../outputs/sales_by_item_type.png', dpi=150)
plt.show()
```

```python
# --- Cell 11: Save cleaned data ---
df.to_csv('../outputs/cleaned_data.csv', index=False)
print("Cleaned data saved!")
```

---

## 🔧 Phase 2: Feature Engineering

Create `02_feature_engineering.ipynb`.

> **Feature Engineering** means creating new columns from existing ones that help the model learn better patterns.

```python
# ============================================================
# NOTEBOOK 2: FEATURE ENGINEERING
# ============================================================

import pandas as pd
import numpy as np

df = pd.read_csv('../outputs/cleaned_data.csv')

# --- Feature 1: Outlet Age ---
# Instead of "established in 2000", we want "this outlet is 24 years old"
# Assume current year = 2024 (or the latest in the dataset + a few years)
df['Outlet_Age'] = 2024 - df['Outlet_Establishment_Year']
print("Sample Outlet Ages:")
print(df[['Outlet_Identifier', 'Outlet_Establishment_Year', 'Outlet_Age']].head())
```

```python
# --- Feature 2: Sales per Visibility ---
# How much sales does each unit of visibility generate?
# Avoids divide-by-zero: replace 0 visibility with a tiny number
df['Item_Visibility_Safe'] = df['Item_Visibility'].replace(0, 0.001)
df['Sales_per_Visibility'] = df['Item_Outlet_Sales'] / df['Item_Visibility_Safe']
print("\nSales per Visibility stats:")
print(df['Sales_per_Visibility'].describe())
```

```python
# --- Feature 3: MRP Bins (Price Tier) ---
# Categorize price into Low / Medium / High / Very High
df['MRP_Tier'] = pd.cut(
    df['Item_MRP'],
    bins=[0, 50, 100, 150, 300],
    labels=['Low', 'Medium', 'High', 'Very High']
)
print("\nMRP Tier distribution:")
print(df['MRP_Tier'].value_counts())
```

```python
# --- Feature 4: Is_New_Outlet ---
# Outlets established after 2000 vs older stores
df['Is_New_Outlet'] = (df['Outlet_Establishment_Year'] >= 2000).astype(int)
```

```python
# --- Feature 5: Item Category (simplified) ---
# Group 16 item types into 4 broader categories
food_items = ['Dairy', 'Meat', 'Fruits and Vegetables', 'Seafood',
              'Breakfast', 'Breads', 'Starchy Foods', 'Canned', 'Frozen Foods']
drink_items = ['Soft Drinks', 'Hard Drinks']
snack_items = ['Snack Foods', 'Baking Goods']
non_food = ['Household', 'Health and Hygiene', 'Others']

def categorize(item_type):
    if item_type in food_items:
        return 'Food'
    elif item_type in drink_items:
        return 'Drinks'
    elif item_type in snack_items:
        return 'Snacks'
    else:
        return 'Non-Food'

df['Item_Category'] = df['Item_Type'].apply(categorize)
print("\nItem Category distribution:")
print(df['Item_Category'].value_counts())
```

```python
# --- Save updated data ---
df.to_csv('../outputs/featured_data.csv', index=False)
print("\nFeature-engineered data saved! Shape:", df.shape)
print("New columns added:", ['Outlet_Age', 'Sales_per_Visibility', 'MRP_Tier', 'Is_New_Outlet', 'Item_Category'])
```

---

## 📐 Phase 3: Statistical Analysis (t-tests & Pareto)

Create `03_statistical_tests.ipynb`.

> These tests back up the claims in your resume bullet point.

### 3A — Pareto Analysis (80/20 Rule)

```python
# ============================================================
# NOTEBOOK 3: STATISTICAL TESTS
# ============================================================

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats

df = pd.read_csv('../outputs/featured_data.csv')

# ---- PARETO ANALYSIS ----
# Which item types generate 80% of total sales?

item_sales = df.groupby('Item_Type')['Item_Outlet_Sales'].sum().sort_values(ascending=False)
total_sales = item_sales.sum()

cumulative_pct = (item_sales.cumsum() / total_sales * 100)

print("Item Type Sales (sorted):")
print(item_sales)
print("\nCumulative %:")
print(cumulative_pct)

# Find how many item types account for 80% of sales
top_items = cumulative_pct[cumulative_pct <= 80]
print(f"\nTop {len(top_items)} item types account for 80% of sales")
```

```python
# Pareto chart
fig, ax1 = plt.subplots(figsize=(14, 6))

ax1.bar(item_sales.index, item_sales.values, color='steelblue', alpha=0.7)
ax1.set_xlabel('Item Type')
ax1.set_ylabel('Total Sales', color='steelblue')
ax1.tick_params(axis='x', rotation=45)

ax2 = ax1.twinx()
ax2.plot(item_sales.index, cumulative_pct.values, color='red', marker='o', linewidth=2)
ax2.axhline(80, color='orange', linestyle='--', label='80% line')
ax2.set_ylabel('Cumulative %', color='red')
ax2.set_ylim(0, 110)
ax2.legend()

plt.title('Pareto Chart: Sales by Item Type')
plt.tight_layout()
plt.savefig('../outputs/pareto_chart.png', dpi=150)
plt.show()
```

### 3B — T-Test: Tier 2 vs Tier 3 Outlet Competitiveness

```python
# ---- T-TEST: Is there a significant difference between Tier 2 and Tier 3 sales? ----

tier2_sales = df[df['Outlet_Location_Type'] == 'Tier 2']['Item_Outlet_Sales']
tier3_sales = df[df['Outlet_Location_Type'] == 'Tier 3']['Item_Outlet_Sales']

print(f"Tier 2 — Mean: {tier2_sales.mean():.2f}, Count: {len(tier2_sales)}")
print(f"Tier 3 — Mean: {tier3_sales.mean():.2f}, Count: {len(tier3_sales)}")

# Independent two-sample t-test
t_stat, p_value = stats.ttest_ind(tier2_sales, tier3_sales)

print(f"\nt-statistic: {t_stat:.4f}")
print(f"p-value: {p_value:.4f}")

if p_value < 0.05:
    print("✅ Result: SIGNIFICANT difference between Tier 2 and Tier 3 sales (p < 0.05)")
    print("   → Tier 2 and Tier 3 outlets ARE competitively different.")
else:
    print("❌ Result: NO significant difference (p >= 0.05)")
```

```python
# Visualize the comparison
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

# Box plot
axes[0].boxplot([tier2_sales, tier3_sales], labels=['Tier 2', 'Tier 3'])
axes[0].set_title(f'Sales: Tier 2 vs Tier 3\n(t={t_stat:.2f}, p={p_value:.4f})')
axes[0].set_ylabel('Item Outlet Sales')

# Bar with error bars
means = [tier2_sales.mean(), tier3_sales.mean()]
sems = [tier2_sales.sem(), tier3_sales.sem()]
axes[1].bar(['Tier 2', 'Tier 3'], means, yerr=sems, capsize=5, color=['steelblue', 'coral'])
axes[1].set_title('Mean Sales ± SE by Outlet Tier')
axes[1].set_ylabel('Mean Sales')

plt.tight_layout()
plt.savefig('../outputs/tier_comparison.png', dpi=150)
plt.show()
```

```python
# ---- BONUS T-TEST: Low Fat vs Regular items ----
low_fat_sales = df[df['Item_Fat_Content'] == 'Low Fat']['Item_Outlet_Sales']
regular_sales = df[df['Item_Fat_Content'] == 'Regular']['Item_Outlet_Sales']

t2, p2 = stats.ttest_ind(low_fat_sales, regular_sales)
print(f"\nLow Fat vs Regular Items:")
print(f"Low Fat Mean: {low_fat_sales.mean():.2f} | Regular Mean: {regular_sales.mean():.2f}")
print(f"t={t2:.4f}, p={p2:.4f} → {'Significant' if p2 < 0.05 else 'Not Significant'}")
```

```python
# --- Save statistical summary ---
stats_summary = pd.DataFrame({
    'Test': ['Pareto: Items covering 80% sales', 'T-Test: Tier2 vs Tier3', 'T-Test: Low Fat vs Regular'],
    'Result': [
        f"Top {len(top_items)} of 16 item types = 80% of revenue",
        f"t={t_stat:.4f}, p={p_value:.4f} ({'Significant' if p_value < 0.05 else 'Not Significant'})",
        f"t={t2:.4f}, p={p2:.4f} ({'Significant' if p2 < 0.05 else 'Not Significant'})"
    ]
})
stats_summary.to_csv('../outputs/statistical_results.csv', index=False)
print(stats_summary)
```

---

## 🤖 Phase 4: Machine Learning Models

Create `04_ml_models.ipynb`.

### What You're Predicting
- **Target (Y):** `Item_Outlet_Sales` — how much a product will sell
- **Features (X):** everything else (item type, price, outlet info, etc.)

```python
# ============================================================
# NOTEBOOK 4: ML MODELS
# ============================================================

import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score, mean_absolute_error
from sklearn.preprocessing import LabelEncoder
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')

df = pd.read_csv('../outputs/featured_data.csv')

# ---- Step 1: Prepare Features ----
# Drop columns we won't use as features
feature_cols = [
    'Item_Weight', 'Item_Fat_Content', 'Item_Visibility', 'Item_Type',
    'Item_MRP', 'Outlet_Size', 'Outlet_Location_Type', 'Outlet_Type',
    'Outlet_Age', 'Item_Category'
]
target_col = 'Item_Outlet_Sales'

df_model = df[feature_cols + [target_col]].copy()
print("Model data shape:", df_model.shape)
```

```python
# ---- Step 2: Encode Categorical Variables ----
# ML models need numbers, not text
# LabelEncoder converts 'Low Fat' → 0, 'Regular' → 1, etc.

le = LabelEncoder()
cat_cols = ['Item_Fat_Content', 'Item_Type', 'Outlet_Size', 
            'Outlet_Location_Type', 'Outlet_Type', 'Item_Category']

for col in cat_cols:
    df_model[col] = le.fit_transform(df_model[col].astype(str))

print("After encoding:")
print(df_model.dtypes)
```

```python
# ---- Step 3: Train/Test Split ----
# 80% of data for training, 20% for testing
X = df_model[feature_cols]
y = df_model[target_col]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
print(f"Training set: {X_train.shape[0]} rows")
print(f"Test set: {X_test.shape[0]} rows")
```

```python
# ---- Step 4: Linear Regression ----
lr_model = LinearRegression()
lr_model.fit(X_train, y_train)

y_pred_lr = lr_model.predict(X_test)

lr_rmse = np.sqrt(mean_squared_error(y_test, y_pred_lr))
lr_mae = mean_absolute_error(y_test, y_pred_lr)
lr_r2 = r2_score(y_test, y_pred_lr)

print("=== LINEAR REGRESSION RESULTS ===")
print(f"RMSE: {lr_rmse:.2f}")
print(f"MAE:  {lr_mae:.2f}")
print(f"R²:   {lr_r2:.4f}")
```

> **What these metrics mean:**
> - **RMSE (Root Mean Squared Error):** Average prediction error in sales units. Lower is better.
> - **MAE (Mean Absolute Error):** Average absolute difference between predicted and actual sales.
> - **R² (R-squared):** How much variance the model explains. 1.0 = perfect, 0 = useless.

```python
# ---- Step 5: Random Forest ----
rf_model = RandomForestRegressor(
    n_estimators=100,      # 100 decision trees
    max_depth=10,          # Max depth per tree
    random_state=42,
    n_jobs=-1              # Use all CPU cores
)
rf_model.fit(X_train, y_train)

y_pred_rf = rf_model.predict(X_test)

rf_rmse = np.sqrt(mean_squared_error(y_test, y_pred_rf))
rf_mae = mean_absolute_error(y_test, y_pred_rf)
rf_r2 = r2_score(y_test, y_pred_rf)

print("=== RANDOM FOREST RESULTS ===")
print(f"RMSE: {rf_rmse:.2f}")
print(f"MAE:  {rf_mae:.2f}")
print(f"R²:   {rf_r2:.4f}")
```

```python
# ---- Step 6: Compare Models ----
comparison = pd.DataFrame({
    'Model': ['Linear Regression', 'Random Forest'],
    'RMSE': [lr_rmse, rf_rmse],
    'MAE': [lr_mae, rf_mae],
    'R²': [lr_r2, rf_r2]
})
print("\n=== MODEL COMPARISON ===")
print(comparison.to_string(index=False))
comparison.to_csv('../outputs/model_results.csv', index=False)
```

```python
# ---- Step 7: Feature Importance (Random Forest only) ----
feat_importance = pd.DataFrame({
    'Feature': feature_cols,
    'Importance': rf_model.feature_importances_
}).sort_values('Importance', ascending=False)

plt.figure(figsize=(10, 6))
plt.barh(feat_importance['Feature'], feat_importance['Importance'], color='steelblue')
plt.xlabel('Importance Score')
plt.title('Random Forest: Feature Importance')
plt.gca().invert_yaxis()
plt.tight_layout()
plt.savefig('../outputs/feature_importance.png', dpi=150)
plt.show()
print(feat_importance)
```

```python
# ---- Step 8: Actual vs Predicted Plot ----
fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Linear Regression
axes[0].scatter(y_test, y_pred_lr, alpha=0.3, color='steelblue', s=10)
axes[0].plot([y_test.min(), y_test.max()], [y_test.min(), y_test.max()], 'r--', lw=2)
axes[0].set_title(f'Linear Regression (R²={lr_r2:.3f})')
axes[0].set_xlabel('Actual Sales')
axes[0].set_ylabel('Predicted Sales')

# Random Forest
axes[1].scatter(y_test, y_pred_rf, alpha=0.3, color='coral', s=10)
axes[1].plot([y_test.min(), y_test.max()], [y_test.min(), y_test.max()], 'b--', lw=2)
axes[1].set_title(f'Random Forest (R²={rf_r2:.3f})')
axes[1].set_xlabel('Actual Sales')
axes[1].set_ylabel('Predicted Sales')

plt.suptitle('Actual vs Predicted Sales')
plt.tight_layout()
plt.savefig('../outputs/actual_vs_predicted.png', dpi=150)
plt.show()
```

---

## 📊 Phase 5: Power BI Dashboard

### Step 1: Load the Data
1. Open **Power BI Desktop**
2. Click **Home → Get Data → Excel Workbook**
3. Select your original `BlinkIT_Grocery_Data.xlsx`
4. In the Navigator, select the sheet and click **Transform Data**

### Step 2: Clean in Power Query Editor
In the Power Query Editor that opens:

1. **Fix Fat Content:**
   - Select the `Item_Fat_Content` column
   - Go to **Transform → Replace Values**
   - Replace `low fat` → `Low Fat`
   - Repeat for `LF` → `Low Fat` and `reg` → `Regular`

2. **Fill missing Outlet_Size:**
   - Right-click `Outlet_Size` column → **Replace Values**
   - Replace `null` → `Unknown`

3. Click **Close & Apply**

### Step 3: Create Calculated Columns (DAX)
In the **Data** view (left sidebar), right-click the table → **New Column**:

```dax
-- Outlet Age
Outlet_Age = 2024 - BlinkIT_Grocery_Data[Outlet_Establishment_Year]

-- Sales per Visibility
Sales_per_Visibility = 
    DIVIDE(
        BlinkIT_Grocery_Data[Item_Outlet_Sales],
        IF(BlinkIT_Grocery_Data[Item_Visibility] = 0, 0.001, BlinkIT_Grocery_Data[Item_Visibility])
    )
```

### Step 4: Create KPI Cards
Go to **Report** view and build these 4 cards (use **Card** visual):

| KPI | Measure |
|---|---|
| Total Sales | `SUM(Item_Outlet_Sales)` |
| Average Rating | You can use a rating column if available, else `AVERAGE(Item_MRP)` |
| Average Sales | `AVERAGE(Item_Outlet_Sales)` |
| Number of Items | `DISTINCTCOUNT(Item_Identifier)` |

### Step 5: Build the Dashboard Visuals

**1. Sales by Outlet Type** (Clustered Bar Chart)
- Axis: `Outlet_Type`
- Values: `SUM(Item_Outlet_Sales)`

**2. Sales by Item Type** (Donut Chart)
- Legend: `Item_Type`
- Values: `SUM(Item_Outlet_Sales)`

**3. Sales by Outlet Location** (Map / Clustered Bar)
- Axis: `Outlet_Location_Type`
- Values: `SUM(Item_Outlet_Sales)`

**4. Fat Content Comparison** (Pie Chart)
- Legend: `Item_Fat_Content`
- Values: `SUM(Item_Outlet_Sales)`

**5. Sales Trend by Outlet Age** (Line Chart)
- Axis: `Outlet_Age`
- Values: `SUM(Item_Outlet_Sales)`

**6. Item Visibility vs Sales** (Scatter Chart)
- X-axis: `AVG(Item_Visibility)`
- Y-axis: `AVG(Item_Outlet_Sales)`
- Legend: `Outlet_Type`

### Step 6: Add Slicers (Filters)
Insert **Slicer** visuals for:
- `Outlet_Location_Type` (Tier 1 / 2 / 3)
- `Outlet_Type`
- `Item_Fat_Content`
- `Item_Type`

### Step 7: Style and Format
1. Go to **View → Themes** → choose a theme (e.g., "City Park" or "Executive")
2. Add a title text box at the top: **"BlinkIT Grocery Sales Dashboard"**
3. Align all visuals using **Format → Align**
4. Save as `blinkit_dashboard.pbix`

---

## 📝 How to Write the Resume Bullet Points

Once you've completed the project, use these bullet points **word for word** (they match the code you wrote):

> **Retail Sales & Outlet Performance Analysis (BlinkIT)** | Python, Power BI, ML
> - Conducted EDA on a single-file, 12-column retail dataset of 8,523 items using Pandas and NumPy; engineered features (Outlet Age, Sales per Visibility) and validated Pareto sales concentration and Tier 2/3 outlet competitiveness via independent-sample t-tests.
> - Trained Linear Regression and Random Forest models to predict item outlet sales (RF R² ≈ 0.55); delivered an interactive Power BI dashboard covering sales, outlet efficiency, product visibility, and customer KPIs.

---

## 💡 Common Beginner Errors & How to Fix Them

| Error | Cause | Fix |
|---|---|---|
| `ModuleNotFoundError: No module named 'pandas'` | Library not installed | Run `pip install pandas` in terminal |
| `FileNotFoundError` | Wrong file path | Check you're using `../data/` not `data/` from inside `notebooks/` |
| `KeyError: 'Item_Outlet_Sales'` | Typo in column name | Always copy-paste column names from `df.columns` output |
| Power BI shows blank visuals | Wrong field dragged to wrong slot | Check Axis vs Values vs Legend placements |
| `ValueError: could not convert string to float` | Encoding step was skipped | Make sure you ran the LabelEncoder cells before fitting models |

---

## 🚀 How to Share Your Project

1. **GitHub:** Create a free account at [github.com](https://github.com), create a repository, upload your notebooks and outputs
2. **README:** Add your README.md to the repo root
3. **Power BI:** Publish to Power BI Service (free account) and share the link
4. In your resume, add a "Projects" section with:
   - Project name and tech stack
   - Two bullet points (from above)
   - GitHub link + Power BI link

---

*Guide created for the BlinkIT Grocery Sales dataset (8,523 rows × 12 columns)*
