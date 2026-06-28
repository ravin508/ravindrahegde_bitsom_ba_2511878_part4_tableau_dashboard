# Chart Selection & Visualization Design Justification

**Student**: Ravindra Hegde (Ravi)  
**Course**: BITSOM BA (Business Analytics)  
**Assignment**: Part 4 – Tableau Executive Dashboard  
**Date**: June 2026

---

## 1. Introduction

This document explains the **chart selection** and **visualization design decisions** made while building the Executive Sales Dashboard. Every chart was chosen based on its ability to answer specific business questions effectively, while following visualization best practices such as clarity, minimal clutter, proper color usage, and business interpretability.

---

## 2. Design Principles Applied

The following visualization design principles were followed throughout the dashboard:

| Principle                    | How It Was Applied                                      |
|-----------------------------|----------------------------------------------------------|
| **Correct Chart Selection** | Charts were chosen based on the type of data and the question being answered (comparison, trend, distribution, relationship) |
| **Clear Hierarchy**         | Most important information (KPIs) placed at the top; supporting analysis placed below |
| **Minimal Clutter**         | Removed unnecessary gridlines, labels, and decorations. Used white space effectively |
| **Consistent Color Usage**  | Same color palette used across sheets (Blue = 2024, Orange = 2025, Green/Red for profit margin) |
| **Proper Labels & Titles**  | Every chart has a clear, business-friendly title and axis labels |
| **Appropriate Sorting**     | All bar charts sorted by the most relevant metric (usually Sales or Return Rate descending) |
| **Useful Filters**          | Filters added for Region, Category, Ship Mode, and Year to enable interactive analysis |
| **Avoidance of Misleading Scales** | Dual-axis charts were synchronized. No truncated axes used |
| **Business Interpretation** | Every view focuses on actionable insights rather than just displaying data |

---

## 3. Chart Justifications

### 3.1 KPI Scorecard

**Chart Type**: Text-based KPI Cards

**Justification**:
- Executives need to see the most critical metrics at a single glance.
- Text-based KPI cards are the fastest way to communicate key numbers (Total Sales, Total Profit, Profit Margin %, Total Orders, Return Rate).
- Avoids visual clutter while providing immediate status of business health.

**Design Decision**:
- Large numbers with clear labels
- Profit Margin % and Return Rate formatted as percentages
- Consistent alignment and spacing

---

### 3.2 Sales Trend View

**Chart Type**: Dual Axis (Bar + Line)

**Justification**:
- The business question is: *"How are Sales and Profit performing over time, and how do they compare year-over-year?"*
- Bars are excellent for comparing discrete time periods (months).
- Line is effective for showing the trend of Profit over time.
- Dual Axis allows comparison of two measures with different scales (Sales vs Profit).
- Year-over-year comparison using color (Blue = 2024, Orange = 2025) enables easy trend analysis.

**Design Decision**:
- Month Name on X-axis for clear time readability
- Synchronized dual axis to avoid misleading scales
- Clear legend and title

---

### 3.3 Regional Performance View

**Chart Type**: Horizontal Bar Chart

**Justification**:
- The business question is: *"Which regions are performing well in terms of sales and profitability?"*
- Horizontal bars are ideal when comparing categories with long labels (Region names).
- Easy to sort and compare performance across regions.
- Color by Profit (or Profit Margin %) allows quick identification of high-profit vs low-profit regions.

**Design Decision**:
- Sorted by Sales (descending)
- Color encoding used to highlight profitability
- Profit Margin % added to tooltip for deeper analysis

---

### 3.4 Category Profitability View

**Chart Type**: Horizontal Bar Chart

**Justification**:
- The business question is: *"Which categories and sub-categories are driving sales, and more importantly, which are profitable?"*
- Horizontal bar chart allows clear comparison of many sub-categories.
- Coloring by **Profit Margin %** directly highlights profitability issues (green = profitable, red = loss-making).
- This view helps identify "high sales but low profit" sub-categories, which is critical for decision-making.

**Design Decision**:
- Sub-Category on Rows for detailed view
- Category used on Color or Detail for grouping
- Sorted by Sales or Profit Margin % depending on focus

---

### 3.5 Customer Segment View

**Chart Type**: Grouped Bar Chart (Side-by-Side)

**Justification**:
- The business question is: *"How do different customer segments (Consumer, Corporate, Home Office) compare in terms of sales and profitability?"*
- Grouped (side-by-side) bars allow direct comparison of Sales and Profit for each segment.
- Adding **Profit Margin %** as color or in tooltip provides quick insight into which segment is most profitable.

**Design Decision**:
- Clear grouping of segments
- Profit Margin % highlighted (most important metric for this view)
- Supporting metrics (Avg Order Value, Return Rate) placed in tooltip

---

### 3.6 Shipping Performance View (Optional / Supporting)

**Chart Type**: Bar Chart colored by Shipping Delay Bucket

**Justification**:
- The business question is: *"How do different shipping modes perform in terms of delivery speed and business outcomes?"*
- Coloring by `Shipping Delay Bucket` makes it easy to see which ship modes are fast vs slow.
- Adding Return Rate and Profit Margin % in tooltip helps understand the business impact of shipping choices.

---

## 4. Overall Dashboard Design Decisions

| Element                    | Decision Made                                      | Reason |
|---------------------------|----------------------------------------------------|--------|
| **Layout**                | KPI Scorecard on top, followed by trend and comparison views | Follows executive reading pattern (summary first, then details) |
| **Color Palette**         | Consistent use of Blue (2024), Orange (2025), Green/Red (Profit Margin) | Easy year-over-year comparison and clear profitability signaling |
| **Filters**               | Region, Category, Ship Mode, Year                  | Most relevant business dimensions for filtering |
| **Interactivity**         | Filter actions across sheets                       | Allows users to drill down from high-level to detailed views |
| **Sorting**               | All bar charts sorted by Sales or Return Rate (descending) | Helps users quickly identify top/bottom performers |
| **Labels & Titles**       | Clear, descriptive titles on every sheet           | Improves readability and business context |
| **Clutter Reduction**     | Removed unnecessary gridlines, borders, and labels | Focuses attention on the data |

---

## 5. Conclusion

Every chart in the Executive Dashboard was deliberately chosen to answer specific business questions while following visualization best practices. The design prioritizes:

- **Clarity** over decoration
- **Actionable insights** over raw data display
- **Consistency** in color and layout
- **Interactivity** to support exploration

This approach ensures that the dashboard is not just visually appealing, but genuinely useful for retail leadership to monitor performance, identify opportunities, and make data-driven decisions.

---

*Document prepared as part of BITSOM BA Assignment – Part 4: Tableau Executive Dashboard*