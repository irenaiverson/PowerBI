# Power BI Portfolio Project: YTD vs PYTD Sales Analysis

## Project Overview

This Power BI project analyzes Year-to-Date (YTD) vs Prior Year-to-Date (PYTD) performance, providing insights into Sales, Quantity, and Gross Profit. The interactive dashboard includes slicers for dynamic filtering, allowing users to view performance across different metrics.
Dataset & Data Model

The analysis is based on Plant Data, structured into the following tables:
- Fact_Sales – Contains transactional sales data.
- Dim_Account – Stores account details.
- Dim_Product – Originally named "Plant Hierarchy," this table holds product-related information.
- Dim_Date – A new Date Table created using CALENDAR DAX to support time intelligence functions.
- Slc_Values – A helper table to enable slicer-based selection.

## Data Model Relationship
- Fact_Sales is linked to Dim_Account via Account_ID.
- Fact_Sales is linked to Dim_Product via Product_ID.
- Fact_Sales is linked to Dim_Date via Date_Time.

This structure enables proper YTD calculations and ensures consistent time-based comparisons.

## Key Features
- Dynamic YTD vs PYTD Analysis – Ensures partial-year comparisons align correctly with previous years using DAX date calculations.
- Year Selection for 2022 and 2023 – The report allows users to toggle between years dynamically
- Custom DAX-Based Slicer for KPI Selection – A special slicer enables switching between Sales, Quantity, and Gross Profit across all visuals.
  
## Visualizations:

1. Waterfall Chart – Displays monthly changes in YTD vs PYTD.
2. Stacked Column & Line Chart – Shows monthly YTD trends.
3. Treemap – Highlights the Bottom 10 Countries by performance.
4. Scatter Plot – Segments accounts based on GP% vs Sales.

## DAX Calculations
1. Handling partial year to date measures.
- To compare current year sales with the same period last year, Inpast DAX calculations were applied to ensure correct date-based filtering. (Ensures that PYTD includes only the same period as the current YTD.

```Inpast = VAR lastsalesdate = MAX(Fact_Sales[Date_Time]) VAR lastsalesdatePY = EDATE(lastsalesdate, -12) RETURN Dim_Date[Date] <= lastsalesdatePY```


2. PYTD Sales calculation.
To compute PYTD Sales, I used SAMEPERIODLASTYEAR, but with an additional filter to respect the Inpast measure:

```PYTD_Sales = CALCULATE( [Sales], SAMEPERIODLASTYEAR(Dim_Date[Date]), Dim_Date[Inpast] = TRUE() )```

3. Dynamic title measure 
This measure ensures that when a user selects a metric from the slicer (Sales, Quantity, or Gross Profit), the title of the visualization automatically updates to reflect the chosen KPI, providing a clear and interactive reporting experience.

```Column chart title = SELECTEDVALUE(Slc_values[Values]) & "YTD vs PYTD | Month"```

4. Dynamic slicer selection.
Power BI has built-in slicers, but they cannot switch between different measures (e.g., Sales, Quantity, Gross Profit). Instead, we created a disconnected table and used DAX to dynamically select the chosen metric.
This approach ensures that all visuals update correctly when switching between KPIs. 

First of all, a slicer table was created. The slicer table contains predefined options:
```SlicerTable = DATATABLE("Values", STRING,{{"Sales"},{"Quantity"},{"Gross Profit"}})```

This measure allows to select between different KPIs (Sales, Quantity, Gross Profit) using a slicer. In other words, users can seamlessly switch between different metrics without modifying the report structure

S_PYTD = 
VAR selected_value = SELECTEDVALUE(Slc_values[Values])
VAR result = SWITCH(selected_value,
    "Sales", [PYTD_Sales],
    "Quantity", [PYTD_Quantity],
    "Gross Profit", [PYTD_GrossProfit],
    BLANK()
)
RETURN result

### Insights & Findings
- YTD Performance: The total YTD sales amount is $13M, with a GP% of 39.62%.
- PYTD Comparison: Sales are down by $512K compared to the prior year.
- Country-Level Analysis: China, Sweden, and France are among the worst-performing regions.
- Product-Type Trends: Indoor, Landscape, and Outdoor categories exhibit distinct performance patterns.

## Technical Highlights
- Power BI DAX for date-based analysis.
- Data Model with Dimensional Tables to enable better performance and accurate filtering.
- Multiple data sources integrated for a comprehensive analysis.
- Custom DAX-based slicer for KPI switching to dynamically change between Sales, Quantity, and Gross Profit.
- Year selection functionality for toggling between 2022 and 2023 using a standard slicer.
