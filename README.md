# Part 4: Tableau Executive Dashboard & Data Storytelling
**Student**: Ravindra Hegde
**Course**: BITSOM BA (Business Analytics)  
**Date**: June 2026

---

## Task 1: Connect and Inspect Data

### Data Source
- **File**: `dashboard_sales_data.xlsx`
- **Sheets**:
  1. `dashboard_sales_data` — Main transactional dataset (4,200 rows × 20 columns)
  2. `data_dictionary` — Column descriptions (provided)

### Data Inspection Summary (using pandas)

**Shape**: 4,200 orders (clean, no duplicate `order_id`)

**Columns & Types** (after pandas/Excel load):
- **Date fields**: `order_date`, `ship_date` → `datetime64[ns]` (correctly parsed from Excel serial dates)
- **Geographic fields**: `region` (4), `state` (multiple Indian states), `city`
- **Categorical fields**: 
  - `customer_segment` (Consumer, Corporate, Home Office)
  - `category` (Furniture, Office Supplies, Technology)
  - `sub_category` (13 values)
  - `ship_mode` (Second Class, Standard Class, First Class, Same Day)
  - `campaign_channel` (Organic, Paid, Email, Social, Referral + nulls)
  - `product_name` (4,111 unique — very high cardinality)
- **Numerical measures**: `sales` (float, right-skewed), `quantity` (int 1-10), `discount` (0.00-0.35), `profit` (float, can be negative), `delivery_days` (int 0-9), `customer_rating` (float 2.1-5.0)
- **Binary/Flag**: `return_flag` (0 = not returned, 1 = returned)

**Key Statistics**:
- **Date Range**: Orders 2024-01-01 to 2025-12-31 (full two calendar years, balanced monthly distribution ~140-200 orders/month)
- **Ship dates**: Up to 2026-01-06 (a few late-2025 orders shipped early 2026 — included as complete view)
- **Missing Values** (small impact):
  - `customer_rating`: 32 rows (0.76%)
  - `campaign_channel`: 24 rows (0.57%)
- **Returns**: 4.55% of orders (`return_flag=1`)
- **Profitability**: Mean profit ~₹7,930 but high variance; many negative profit orders (losses), especially Furniture with higher discounts (0.25-0.30)
- **Delivery**: `delivery_days` perfectly matches `(ship_date - order_date).days` (no data quality issues)
- **Sales**: Highly skewed (mean ~₹51,671, max ~₹427k); top states by volume: Rajasthan, Telangana, Kerala, Tamil Nadu, etc.

**Data Quality Verdict**: Excellent overall. No duplicates, consistent calculated fields, sensible ranges, minimal missing data. Ready for Tableau.

### Assumptions & Notes for Tableau Connection

1. **Date Handling**:
   - Tableau will correctly interpret `order_date` and `ship_date` as dates (Excel serial date format is standard and parses reliably).
   - Created/verified: `delivery_days` = `DATEDIFF('day', [order_date], [ship_date])` matches source exactly.
   - Recommendation: Create calculated fields in Tableau:
     - `Year` = `YEAR([order_date])`
     - `Quarter` = `QUARTER([order_date])`
     - `Month` = `MONTH([order_date])` or `DATENAME('month', [order_date])`
     - `YoY Comparison` using LOD or parameter for dynamic 2024 vs 2025.

2. **Geographic Fields**:
   - `region` (North/West/South/East) is clean primary dimension — use for hierarchy and filtering.
   - `state` and `city` are Indian locations. Tableau's built-in India map recognizes many states but city-level geocoding may be incomplete or inconsistent without custom latitude/longitude (not in source data).
   - **Assumption/Recommendation**: 
     - Prioritize **region + state** hierarchy in dashboard.
     - Use bar charts / treemaps for state/city performance instead of (or alongside) maps.
     - If map required: Add external India city lat/long lookup or use symbol map sized by sales. Avoid filled maps for cities to prevent misplacement.

3. **High-Cardinality Fields**:
   - `product_name` (4,111 uniques) → Not suitable for filters, legends, or grouping in most views. Use only in "Details on Demand" (tooltips) or Top N filters (e.g., Top 10 products by sales/profit).
   - Aggregate at `category` > `sub_category` level for executive views.

4. **Missing Data Handling in Tableau**:
   - `campaign_channel` nulls (24 rows): Create calculated field `Campaign Channel Clean` = `IFNULL([campaign_channel], "Unknown")` or group into "Other/Unknown".
   - `customer_rating` nulls (32 rows): Tableau `AVG()` and other aggregations automatically exclude NULLs. Optionally create `Rating Available` flag (Yes/No) or impute with overall mean (~4.06) via calculated field if needed for scoring.
   - **Assumption**: Missingness is random/MCAR and low enough (<1%) to ignore for high-level KPIs without material bias.

5. **Negative Profits & Business Logic**:
   - Negative `profit` values are valid (losses on discounted or returned orders). Do **not** filter them out.
   - Key story driver: Correlation between high `discount` (≥0.25) and negative profit, especially in Furniture/Bookcases/Tables.
   - Create calculated field: `Profit Margin %` = `SUM([profit]) / SUM([sales])` (handle division by zero if any, though none observed).

6. **Return & Shipping Performance**:
   - `return_flag` → Create `Is Returned` (Yes/No) dimension and `Return Rate %` KPI.
   - `delivery_days` → Create bins or thresholds (e.g., "Same Day", "1-3 Days", "4-7 Days", ">7 Days") for performance scoring. No promised delivery date in data, so use average days as proxy for speed/efficiency.

7. **Other Calculated Fields Recommended for Dashboard**:
   - `Profit Margin %`
   - `Avg Order Value (AOV)` = `SUM([sales]) / COUNTD([order_id])`
   - `YoY Sales Growth %` (via LOD or table calc)
   - Binned `Discount Tier`: 0%, 1-10%, 11-20%, 21-30%, 31%+
   - `Order Month Name`, `Fiscal Year` (if needed)
   - `Loss Making Order` flag (profit < 0)

8. **Data Scope & Filtering**:
   - Full 2024 + 2025 data. For "current" executive view, consider parameter-driven filter (e.g., "Last 12 Months" or YTD 2025) vs full historical.
   - A few ship dates in Jan 2026: Include (represents fulfilled late-2025 demand). Add note or optional filter "Shipped by [date]".

9. **Performance Considerations in Tableau**:
   - 4,200 rows is small → Live connection to Excel or Hyper extract is fine. No performance issues expected.
   - For interactivity: Use dashboard filters + actions (e.g., click Region bar → filters Category and State views).
   - Extract (.hyper) recommended for faster authoring if connecting live to large Excel.

### Data Storytelling Themes for Executive Dashboard
The dashboard should **not** be a random collection of charts. It must answer leadership questions and highlight opportunities/risks:

**Core Narrative**:
"Despite strong overall sales growth and high customer ratings (avg 4.06), profitability is being eroded by discount-driven losses in Furniture, elevated return rates in certain segments/regions, and slower shipping in some channels. Organic campaigns drive the highest volume but we need to optimize Paid/Referral for margin. Focus areas: Reduce unprofitable discounts, improve Furniture margin, target high-return categories with better QA/education, and accelerate delivery in underperforming regions."

**Key Sections / Sheets** (proposed):
1. **KPI Scorecard** (top row): Total Sales, Profit, Margin %, Orders, AOV, Return Rate, Avg Delivery Days, Avg Rating (+ sparklines or YoY arrows)
2. **Sales & Profit Trend** (time series): Monthly trend with dual-axis (Sales bars + Profit line), YoY comparison, highlight loss months
3. **Regional Performance**: Map or ranked bars by Region/State — color by Margin % or Return Rate. Identify laggards (e.g., high sales but low margin)
4. **Category & Segment Deep Dive**: Stacked bar / treemap Sales + Profit by Category × Segment. Highlight Furniture losses
5. **Discount vs Profitability Analysis**: Scatter (Discount % vs Profit Margin) colored by Category/Return; or box plots by discount tier. Key insight driver
6. **Returns & Quality**: % Returned by Category/Subcat/Region + top loss-making returned orders (detail table)
7. **Shipping Efficiency**: Avg Delivery Days by Ship Mode × Region + distribution histogram. Identify bottlenecks (e.g., Standard Class in East)
8. **Campaign Effectiveness**: Sales/Profit/AOV by `campaign_channel` (highlight Organic strength vs Paid conversion)
9. **Top Movers**: Top 10 States/Sub-categories by sales or margin contribution (with profit color)

**Interactivity & Usability**:
- Global filters: Year (multi-select), Region, Customer Segment, Category
- Dashboard Actions: Select region → updates all other sheets
- Parameter: "Margin Threshold" to highlight problematic orders
- Tooltips rich with context (e.g., on trend point: prior year comparison, margin, return %)
- Mobile-friendly layout (Tableau uses responsive design; prioritize KPI row + 2-3 key charts)
- Consistent branding: Professional color palette (greens for profit/growth, reds/oranges for losses/returns, blues for neutral), clear titles with auto-updating subtitles (e.g., "Data as of [Max Order Date]")

**Risks & Opportunities Identified from Data** (to surface in dashboard/story points):
- **Risk**: Furniture category shows frequent negative profits at 25-30% discounts. High-ticket items (Copiers, Machines, Phones) drive sales but margin varies.
- **Opportunity**: Organic channel has highest volume and solid margins — scale it. Referral has lower volume but potentially higher quality (check ratings/profit).
- **Risk**: ~4.55% return rate; investigate correlation with high-discount or specific subcats (e.g., Bookcases).
- **Opportunity**: Same Day / First Class shipping has low volume — promote for high-value customers to improve satisfaction (current avg rating already strong at 4.06).
- **Geographic**: Rajasthan leads in order volume; check if margin follows or if it's volume-at-any-cost.

---

## Next Steps (for full submission)
- **Task 2+**: Design wireframes (in Figma/PowerPoint or hand-drawn), build individual worksheets in Tableau with proper formatting/calculated fields, assemble into dashboard with interactivity, add story points or explanatory text boxes, export as .twbx.
- **Data Prep (optional but recommended)**: I can provide a cleaned/prepared Excel with added columns (Year, Quarter, Profit Margin, Campaign Clean, etc.) if it simplifies authoring.
- **Testing**: Validate all KPIs match source (e.g., total sales, return rate) using Tableau "Describe" or validation sheets.

**Contact for questions on data prep or dashboard spec**: Ravi (user context)

---
*This README serves as the official documentation for Task 1 data inspection and assumptions. It also provides the strategic blueprint for building a decision-support executive dashboard rather than a vanity metrics display.*
