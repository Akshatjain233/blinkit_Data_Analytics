# BlinkIT Sales Analysis — Tableau Recreation Guide
## Step-by-Step Instructions for Every Chart in the PDF

---

## Setup: Connect Your Data

1. Open **Tableau Desktop** (or Tableau Public — free at public.tableau.com)
2. Click **Connect → Microsoft Excel**
3. Select `BlinkIT_Grocery_Data.xlsx`
4. Drag the sheet into the canvas → click **Sheet 1** tab at the bottom to start

> **One-time fix before building charts:** Your `Item_Fat_Content` column has messy labels. Fix this in Tableau:
> - Right-click `Item_Fat_Content` in the left panel → **Aliases**
> - Set `LF` and `low fat` → `Low Fat`; set `reg` → `Regular`
> - Click OK

---

## Page 1, Chart 1 — Total Sales by Product Category (Bar Chart)

**What it shows:** Total sales in millions (₹) for each of the 16 item types, sorted descending.

### Steps:
1. **New Sheet** → rename it `Sales by Category`
2. Drag `Item Type` → **Columns**
3. Drag `Item Outlet Sales` → **Rows**
   - It will auto-aggregate as `SUM`. That's correct.
4. **Sort descending:** Click the bar chart icon with the down arrow in the toolbar (or right-click the axis → Sort → Descending by `SUM(Item Outlet Sales)`)
5. **Color each bar differently:**
   - Drag `Item Type` → **Color** (on the Marks card)
6. **Add value labels on bars:**
   - Drag `Item Outlet Sales` → **Label** (on the Marks card)
   - Click `Label` → tick **Show mark labels**
   - Right-click the label pill → **Format** → Number → Custom → type `"₹"#,##0.0,,M` to show millions
7. **Format the Y-axis:**
   - Right-click Y-axis → **Format** → Numbers → Custom → `#,##0.0,,M`
8. **Title:** Double-click the title area → type `Total Sales by Product Category`
9. **Rotate X labels:** Right-click the X-axis → **Format** → Alignment → set Rotation to 45°

---

## Page 1, Chart 2 — Relationship Between Item Price and Sales (Scatter Plot)

**What it shows:** Each dot is one transaction. X = Item MRP, Y = Item Outlet Sales. Red line = trend line. Correlation = 0.568.

### Steps:
1. **New Sheet** → rename it `Price vs Sales Scatter`
2. Change Marks type to **Circle** (dropdown at top of Marks card)
3. Drag `Item MRP` → **Columns**
   - Right-click the pill → **Dimension** (we want individual points, not aggregated)
4. Drag `Item Outlet Sales` → **Rows**
   - Right-click → **Dimension**
5. **Reduce dot size:** Click **Size** on the Marks card → drag slider left to make dots small
6. **Color dots blue:** Click **Color** → pick a medium blue
7. **Add trend line:**
   - Go to menu: **Analytics** tab (left panel, next to Data)
   - Drag **Trend Line** → drop onto the view → choose **Linear**
   - Right-click the trend line → **Edit Trend Lines** → untick "Show confidence bands"
8. **Add correlation annotation:**
   - Go to **Analytics** tab → drag **Reference Line** onto the plot (or use a text annotation)
   - Alternatively: Right-click → **Annotate → Point** → type `Correlation: 0.568`
   - Move the text box to the top-right corner
9. **Axis titles:** Double-click each axis label to rename:
   - X → `Item Maximum Retail Price (MRP)`
   - Y → `Item Outlet Sales`
10. **Title:** `Relationship Between Item Price and Sales`

---

## Page 2, Chart 1 — Total Sales Distribution by Fat Content (Pie Chart)

**What it shows:** 64% Low Fat, 36% Regular — share of total sales.

### Steps:
1. **New Sheet** → rename it `Sales by Fat Content Pie`
2. Change Marks type to **Pie**
3. Drag `Item Fat Content` → **Color**
4. Drag `Item Outlet Sales` → **Angle** (this sizes the slices)
5. **Add percentage labels:**
   - Drag `Item Outlet Sales` → **Label**
   - Right-click the label pill → **Quick Table Calculation → Percent of Total**
   - Click **Label** → tick **Show mark labels** → check **Always show**
   - Format the number: right-click pill → Format → Number → Percentage → 1 decimal
6. **Also label the category names:**
   - Drag `Item Fat Content` → **Label** as well
7. **Colors:** Click **Color** → Edit Colors → set Low Fat to pink/salmon, Regular to blue
8. **Resize the pie:** In the toolbar, change to **Entire View**
9. **Title:** `Total Sales Distribution by Fat Content`

---

## Page 2, Chart 2 — Average Sales and Item Count by Fat Content (Grouped Bar)

**What it shows:** Side-by-side bars for Average Sales (pink) and Item Count (blue) for Low Fat vs Regular.

### Steps:
1. **New Sheet** → rename it `Avg Sales and Count by Fat Content`
2. Drag `Item Fat Content` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** → it becomes `SUM`. Change to **Average:**
   - Right-click the pill → **Measure → Average**
4. **Add a second measure (Item Count):**
   - Hold **Ctrl** and drag `Item Identifier` to the **Rows** shelf next to the first measure
   - Right-click `Item Identifier` → **Measure → Count (Distinct)**
5. Tableau creates two separate charts. To make them share one axis (dual axis):
   - Right-click the right Y-axis → **Synchronize Axis**
   - Right-click again → **Dual Axis**
6. **Color the bars:**
   - Click `AVG(Item Outlet Sales)` Marks card → Color → pink
   - Click `CNTD(Item Identifier)` Marks card → Color → light blue
7. **Add labels:** Drag each measure to **Label** in its respective Marks card
8. **Title:** `Average Sales and Item Count by Fat Content`

---

## Page 2, Chart 3 — Item Visibility vs Sales Performance (Scatter + Color)

**What it shows:** Each dot colored by a "Visibility Segment" value (color scale from purple to yellow).

### Steps:
1. **New Sheet** → rename it `Visibility vs Sales`
2. Marks type → **Circle**
3. Drag `Item Visibility` → **Columns** (right-click → Dimension)
4. Drag `Item Outlet Sales` → **Rows** (right-click → Dimension)
5. **Create a Visibility Segment calculated field:**
   - Click the dropdown arrow next to the search bar (left panel) → **Create Calculated Field**
   - Name: `Visibility Segment`
   - Formula:
     ```
     IF [Item Visibility] < 0.033 THEN "Very Low"
     ELSEIF [Item Visibility] < 0.066 THEN "Low"
     ELSEIF [Item Visibility] < 0.10 THEN "Medium"
     ELSE "High"
     END
     ```
   - Click OK
6. Drag `Visibility Segment` → **Color**
7. Click **Color → Edit Colors** → assign a sequential palette (e.g., purple→teal→green→yellow to match the PDF)
8. **Dot size:** Click **Size** → reduce
9. **Title:** `Item Visibility vs Sales Performance`

---

## Page 2, Chart 4 — Average Sales by Visibility Segment (Bar Chart)

**What it shows:** 4 bars — Very Low (₹2300), Low (₹2279), Medium (₹1936), High (₹337).

### Steps:
1. **New Sheet** → rename it `Avg Sales by Visibility Segment`
2. Make sure you've created the `Visibility Segment` calculated field (see Chart 3 above)
3. Drag `Visibility Segment` → **Columns**
4. Drag `Item Outlet Sales` → **Rows** → change to **Average** (right-click → Measure → Average)
5. **Sort order:** Right-click the X-axis → **Sort** → Manual → drag to order: Very Low, Low, Medium, High
6. **Color each bar:** Drag `Visibility Segment` → **Color** → use matching colors from Chart 3
7. **Add labels:** Drag `AVG(Item Outlet Sales)` → **Label** → format as `₹#,##0`
8. **Title:** `Average Sales by Visibility Segment`

---

## Page 3, Chart 1 — Total Sales by Outlet (Bar Chart)

**What it shows:** Total sales per outlet identifier, sorted descending. OUT027 leads at ₹3.5M.

### Steps:
1. **New Sheet** → rename it `Total Sales by Outlet`
2. Drag `Outlet Identifier` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** (`SUM` is correct)
4. **Sort descending** by SUM of sales
5. **Color by outlet:** Drag `Outlet Identifier` → **Color**
6. **Add labels:** Drag `Item Outlet Sales` → **Label** → format as `₹#,##0.0,,M`
7. **Title:** `Total Sales by Outlet`

---

## Page 3, Chart 2 — Average Sales Per Item by Outlet (Bar Chart)

**What it shows:** Average sales per item per outlet. Same outlets as above but showing efficiency.

### Steps:
1. **New Sheet** → rename it `Avg Sales Per Item by Outlet`
2. Drag `Outlet Identifier` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** → change to **Average**
4. Sort descending
5. **Color:** Drag `Outlet Identifier` → **Color** (use a different palette from Chart 1 — multicolor)
6. **Labels:** Drag `AVG(Item Outlet Sales)` → **Label** → format as `₹#,##0`
7. **Title:** `Average Sales Per Item by Outlet`

---

## Page 3, Chart 3 — Total Sales Distribution by Outlet Type (Pie)

**What it shows:** Supermarket Type1 dominates at 69.5%.

### Steps:
1. **New Sheet** → rename it `Sales Share by Outlet Type`
2. Marks type → **Pie**
3. Drag `Outlet Type` → **Color**
4. Drag `Item Outlet Sales` → **Angle**
5. Drag `Item Outlet Sales` → **Label** → Quick Table Calculation → **Percent of Total**
6. Also drag `Outlet Type` → **Label**
7. **Title:** `Total Sales Distribution by Outlet Type`

---

## Page 3, Chart 4 — Average Sales by Outlet Type (Bar Chart)

**What it shows:** Supermarket Type3 highest (₹3694), Grocery Store lowest (₹340).

### Steps:
1. **New Sheet** → rename it `Avg Sales by Outlet Type`
2. Drag `Outlet Type` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** → change to **Average**
4. **Sort descending**
5. Drag `Outlet Type` → **Color**
6. **Labels:** Drag `AVG(Item Outlet Sales)` → **Label** → format `₹#,##0`
7. **Title:** `Average Sales by Outlet Type`

---

## Page 3, Chart 5 — Weighted Total Sales Distribution by Outlet Type (Pie)

**What it shows:** Same outlet type split but weighted differently — Supermarket Type1 = 45.2%.

### Steps:
1. **Duplicate** the `Sales Share by Outlet Type` sheet (right-click tab → Duplicate)
2. The difference is in how you weight. Create a calculated field:
   - Name: `Weighted Sales`
   - Formula: `[Item Outlet Sales] * [Item MRP] / 1000`
   - (Adjust the formula if needed — the "weighted" view accounts for price level)
3. Replace `SUM(Item Outlet Sales)` in Angle and Label with `SUM(Weighted Sales)`
4. Recalculate Percent of Total on the label
5. **Title:** `Weighted Total Sales Distribution by Outlet Type`

---

## Page 4, Chart 1 — Sales Performance by Location Tier (Grouped Bar)

**What it shows:** For each tier (Tier 1/2/3): Total Sales (M$), Avg Sales per Item, Avg MRP shown as grouped bars.

### Steps:
1. **New Sheet** → rename it `Sales by Location Tier`
2. Drag `Outlet Location Type` → **Columns**
3. Create three measures on Rows:
   - Drag `Item Outlet Sales` → **Rows** (SUM — rename to `Total Sales`)
   - Hold Ctrl + drag `Item Outlet Sales` again → **Average** (rename to `Avg Sales per Item`)
   - Hold Ctrl + drag `Item MRP` → **Average** (rename to `Avg MRP`)
4. Right-click the right axis → **Dual Axis** (to overlay them)
5. Change Marks type to **Bar** for each measure
6. Assign colors: Total Sales = light blue, Avg Sales = pink/red, Avg MRP = green
7. **Labels** on each: drag the respective measure to Label
8. **Title:** `Sales Performance by Location Tier`

---

## Page 4, Chart 2 — Outlet Distribution by Location Tier (Pie)

**What it shows:** Tier 1 = 30%, Tier 2 = 30%, Tier 3 = 40%.

### Steps:
1. **New Sheet** → rename it `Outlets by Location Tier`
2. Marks type → **Pie**
3. Drag `Outlet Location Type` → **Color**
4. Drag `Outlet Identifier` → **Angle** → change to **Count (Distinct)**
5. Drag `CNTD(Outlet Identifier)` → **Label** → Quick Table Calculation → **Percent of Total**
6. Drag `Outlet Location Type` → **Label** too
7. Colors: Tier 1 = light blue, Tier 2 = pink, Tier 3 = yellow
8. **Title:** `Outlet Distribution by Location Tier`

---

## Page 4, Chart 3 — Total Sales by Outlet Size (Bar)

**What it shows:** Medium outlets lead at ₹7.5M. Bars for Medium, High, Small, Unknown.

### Steps:
1. **New Sheet** → rename it `Total Sales by Outlet Size`
2. Drag `Outlet Size` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** (SUM)
4. **Sort descending**
5. Drag `Outlet Size` → **Color**
6. Add labels formatted as `₹#,##0.0,,M`
7. **Title:** `Total Sales by Outlet Size`

---

## Page 4, Chart 4 — Average Sales by Outlet Size (Bar)

**What it shows:** Medium = ₹2682, High = ₹2299, Small = ₹1912, Unknown = ₹1823.

### Steps:
1. **New Sheet** → rename it `Avg Sales by Outlet Size`
2. Same as above but change `Item Outlet Sales` → **Average** instead of SUM
3. Sort descending
4. Different color palette from Chart 3 (pastel/muted tones)
5. Labels formatted as `₹#,##0`
6. **Title:** `Average Sales by Outlet Size`

---

## Page 4, Charts 5 & 6 — Sales by Outlet Age Group

**What it shows:** Outlets grouped into age buckets (0–10, 11–20, 21–30, 30+ years).

### Create the Age Group calculated field first:
- Name: `Outlet Age Group`
- Formula:
  ```
  IF (2024 - [Outlet Establishment Year]) >= 30 THEN "30+ yrs"
  ELSEIF (2024 - [Outlet Establishment Year]) >= 21 THEN "21-30 yrs"
  ELSEIF (2024 - [Outlet Establishment Year]) >= 11 THEN "11-20 yrs"
  ELSE "0-10 yrs"
  END
  ```

**Chart 5 — Total Sales by Outlet Age Group:**
1. Drag `Outlet Age Group` → **Columns**
2. Drag `Item Outlet Sales` → **Rows** (SUM)
3. Color, sort, label as before
4. **Title:** `Total Sales by Outlet Age Group`

**Chart 6 — Average Sales by Outlet Age Group:**
1. Duplicate Chart 5, change SUM → **Average**
2. **Title:** `Average Sales by Outlet Age Group`

---

## Page 5, Chart 1 — Fruits and Vegetables Sales by Outlet (Bar)

**What it shows:** Sales of only Fruits and Vegetables category, broken down per outlet.

### Steps:
1. **New Sheet** → rename it `Fruits & Veg Sales by Outlet`
2. Drag `Outlet Identifier` → **Columns**
3. Drag `Item Outlet Sales` → **Rows** (SUM)
4. **Filter to only Fruits and Vegetables:**
   - Drag `Item Type` → **Filters** shelf
   - Check only `Fruits and Vegetables` → OK
5. Sort descending
6. Color: gradient of greens — drag `Item Outlet Sales` → **Color** → Edit Colors → Green sequential
7. Add labels, format `₹#,##0`
8. **Title:** `Fruits and Vegetables Sales by Outlet`

---

## Page 5, Chart 2 — Seafood Sales by Outlet (Bar)

**What it shows:** Same as above but filtered to Seafood category.

### Steps:
1. **Duplicate** the `Fruits & Veg Sales by Outlet` sheet
2. Right-click `Item Type` in the Filters shelf → **Edit Filter**
3. Uncheck `Fruits and Vegetables`, check `Seafood`
4. Change color palette to red/salmon tones
5. **Title:** `Seafood Sales by Outlet`

---

## Page 5, Chart 3 — Visibility vs Sales: Hidden Gems & Ineffective Placements (Scatter)

**What it shows:** Items classified as "Hidden Gems" (low visibility, high sales — green) and "Ineffective Placements" (high visibility, low sales — red).

### Create a calculated field for classification:
- Name: `Placement Type`
- Formula:
  ```
  IF [Item Visibility] < 0.05 AND [Item Outlet Sales] > 3000 THEN "Hidden Gems"
  ELSEIF [Item Visibility] > 0.10 AND [Item Outlet Sales] < 1000 THEN "Ineffective Placements"
  ELSE "Normal Items"
  END
  ```
  *(Adjust thresholds to match the chart if needed)*

### Steps:
1. **New Sheet** → rename it `Visibility Hidden Gems`
2. Marks type → **Circle**
3. Drag `Item Visibility` → **Columns** (Dimension)
4. Drag `Item Outlet Sales` → **Rows** (Dimension)
5. Drag `Placement Type` → **Color**
6. **Set colors:** Hidden Gems = dark green, Ineffective Placements = red, Normal Items = light blue/grey
7. **Add reference lines** for the quadrant boundaries:
   - Go to **Analytics** tab → drag **Reference Line** → At Value → set X = 0.05 (dashed)
   - Drag another → set X = 0.10 (dashed)
   - Drag one for Y = 1000 (dashed) and Y = 3000 (dashed)
8. **Add annotation:** Right-click top-left quadrant → **Annotate → Area** → type `Low Visibility\nHigh Sales`
9. **Title:** `Visibility vs Sales: Identifying Hidden Gems and Ineffective Placements`

---

## Page 5, Chart 4 — Feature Importance in Sales Prediction (Horizontal Bar)

**What it shows:** Item_MRP most important (0.453), down to Outlet_Location_Type (0.009).

> Note: This chart comes from a machine learning model (Random Forest). You need to run the Python ML notebook first and export the feature importance values, then manually enter them in Tableau.

### Steps:
1. **Create a new data source** in Tableau: an Excel or CSV file with two columns:
   ```
   Feature, Importance
   Item_MRP, 0.453
   Outlet_Type, 0.234
   Item_Visibility, 0.110
   Outlet_Age, 0.061
   Item_Weight, 0.059
   Item_Type, 0.049
   Outlet_Size, 0.015
   Item_Fat_Content, 0.010
   Outlet_Location_Type, 0.009
   ```
2. Connect Tableau to this new CSV
3. Drag `Feature` → **Rows**
4. Drag `Importance` → **Columns**
5. Marks type → **Bar**
6. **Sort descending** by Importance value
7. Color → single blue
8. Add labels with 3 decimal places
9. **Title:** `Feature Importance in Sales Prediction`

---

## Page 5, Chart 5 — Actual vs Predicted Sales (Scatter Plot, R² = 0.563)

**What it shows:** Each dot is a predicted vs actual sales value from the Random Forest model. Red dashed line = perfect prediction.

> This also requires the Python ML output. Export predictions first.

### Export predictions from Python:
```python
# Add this to the end of your ML notebook (Notebook 4)
results_df = pd.DataFrame({
    'Actual_Sales': y_test.values,
    'Predicted_Sales': y_pred_rf
})
results_df.to_csv('../outputs/model_predictions.csv', index=False)
```

### Steps:
1. Connect Tableau to `model_predictions.csv`
2. **New Sheet** → rename it `Actual vs Predicted`
3. Marks type → **Circle**
4. Drag `Actual Sales` → **Columns** (Dimension)
5. Drag `Predicted Sales` → **Rows** (Dimension)
6. Color → purple/violet
7. Size → small
8. **Add the 45° perfect prediction line:**
   - Go to **Analytics** tab → drag **Reference Line** → drop on the pane
   - Change to "Line" at constant value won't work here — instead use a **Trend Line** (Linear)
   - Or: Create a calculated field `Perfect Line = [Actual Sales]` and add as a reference
9. Change the trend line color to red and style to dashed
10. **Add R² annotation:** Right-click → Annotate → Area → type `R² = 0.563`
11. **Title:** `Actual vs Predicted Sales (R² = 0.563)`

---

## Building the Final Dashboard

Once all sheets are done:

1. Click the **New Dashboard** button (bottom tab with grid icon)
2. Set size: **Fixed Size → 1200 × 900** (or Automatic)
3. **Drag sheets** from the left panel onto the canvas
4. Suggested layout:
   - Row 1: KPI numbers (use text objects for ₹18.5M total sales, ₹2181 avg sales, etc.)
   - Row 2: Sales by Category + Price vs Sales Scatter
   - Row 3: Fat Content pie + Outlet Type pie + Avg Sales by Outlet Type
   - Row 4: Sales by Tier + Sales by Size
   - Row 5: Hidden Gems scatter + Feature Importance
5. **Add filters that apply to all sheets:**
   - Click the `Outlet Type` filter on any sheet → More Options → **Apply to Worksheets → All Using This Data Source**
   - Repeat for `Outlet Location Type` and `Item Fat Content`
6. **Add a title:** Insert → Text → type `BlinkIT Grocery Sales & Outlet Performance Dashboard`
7. **Save:** File → Save As → name it `blinkit_dashboard.twbx` (packaged workbook — includes data)

---

## Quick Reference: Chart Summary

| Page | Chart | Type | Key Fields |
|---|---|---|---|
| 1 | Total Sales by Category | Bar | Item Type → Sales SUM |
| 1 | Price vs Sales | Scatter | Item MRP × Item Outlet Sales |
| 2 | Sales by Fat Content | Pie | Item Fat Content → Sales % |
| 2 | Avg Sales & Count by Fat | Grouped Bar | Fat Content × Avg Sales + Count |
| 2 | Visibility vs Sales | Scatter | Item Visibility × Sales (colored) |
| 2 | Avg Sales by Visibility Segment | Bar | Visibility Segment → Avg Sales |
| 3 | Total Sales by Outlet | Bar | Outlet Identifier → Sales SUM |
| 3 | Avg Sales per Item by Outlet | Bar | Outlet Identifier → Avg Sales |
| 3 | Sales Share by Outlet Type | Pie | Outlet Type → Sales % |
| 3 | Avg Sales by Outlet Type | Bar | Outlet Type → Avg Sales |
| 3 | Weighted Sales by Outlet Type | Pie | Outlet Type → Weighted Sales % |
| 4 | Sales by Location Tier | Grouped Bar | Tier × Total/Avg Sales/MRP |
| 4 | Outlets by Location Tier | Pie | Tier → Count % |
| 4 | Total Sales by Outlet Size | Bar | Outlet Size → Sales SUM |
| 4 | Avg Sales by Outlet Size | Bar | Outlet Size → Avg Sales |
| 4 | Total Sales by Age Group | Bar | Outlet Age Group → Sales SUM |
| 4 | Avg Sales by Age Group | Bar | Outlet Age Group → Avg Sales |
| 5 | Fruits & Veg by Outlet | Filtered Bar | Item Type filter → Sales SUM |
| 5 | Seafood by Outlet | Filtered Bar | Item Type filter → Sales SUM |
| 5 | Hidden Gems Scatter | Scatter | Visibility × Sales (Placement Type) |
| 5 | Feature Importance | Horiz. Bar | Feature → Importance (manual CSV) |
| 5 | Actual vs Predicted | Scatter | Actual Sales × Predicted Sales (CSV) |

---

*All charts use the BlinkIT_Grocery_Data.xlsx dataset (8,523 rows × 12 columns). Charts on Page 5 (Feature Importance and Actual vs Predicted) require running the Python ML notebook first to generate output CSVs.*
