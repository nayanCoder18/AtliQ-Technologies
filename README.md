# AtliQ Technologies: End-to-End Excel + SQL + Power BI Project

## Project Overview

- This project is a complete end-to-end data analysis solution designed to extract business insights for AtliQ Technologies, a hospitality chain. The pipeline integrates Excel for raw data cleaning, SQL Server for exploratory data analysis (EDA) and query-based insights, and Power BI for building interactive dashboards.

- The goal is to simulate a real-world data analytics project where multiple tools are combined to prepare, analyze, and visualize data in order to answer business-critical questions.

_________________________________________________________________________________________________________________

### Project Steps

#### 1. Set Up the Environment

  - Tools Used: Excel, Microsoft SQL Server, Power BI

  - Goal: Create a structured workflow starting from raw data cleaning to final dashboard publishing.

### 2. Data Cleaning (Excel)

  - Removed duplicates across all dimension and fact tables.

  - Handled missing values by either imputing or dropping based on context.

  - Fixed data types (dates, integers, floats).

  - Ensured consistency across columns such as property IDs, city names, and booking references.

### 3. Load Data into SQL Server

#### Tables Created

 - `dim_date`

 - `dim_hotels`

 - `dim_rooms`

 - `fact_bookings`

- `fact_aggregated_bookings`

- Verified successful load and integrity using SQL queries.

### 4. Exploratory Data Analysis (EDA) with SQL

- Analyzed booking volumes, occupancy trends, and revenue distribution.

- Used aggregations, joins, subqueries, and window functions to answer questions like:

- Which city generates the highest revenue?

- How do weekdays vs. weekends differ in occupancy?

- Which booking platforms yield the highest realization %?

- What is the trend of RevPAR, ADR, and DSRN across time?

### 5. Visualization in Power BI

- Built an interactive dashboard with multiple KPIs and charts:

 - Key Metrics: Revenue, RevPAR, DSRN, Occupancy, ADR, Realisation %

 - Revenue Split: % revenue by luxury vs. business category

 - Trend Analysis: RevPAR, ADR, and Occupancy % over time

 - Property Comparison: ADR, realization %, cancellation rate, and ratings

 - Platform Analysis: Realisation % and ADR by platform
_________________________________________________________________________________________________________________

### Requirements

 - Excel (Office 365 or later)

 - SQL Server (2019 or later)
_________________________________________________________________________________________________________________

 - Power BI Desktop
```
Project Structure
|-- data/                      # Raw and cleaned datasets  
|-- sql_queries/                # SQL scripts for analysis and transformations  
|-- powerbi_dashboard/          # Power BI (.pbix) file  
|-- README.md                   # Project documentation
```
_________________________________________________________________________________________________________________

### Results and Insights

Key findings from the analysis:

 - **Occupancy:** Average occupancy stands at **~57.8%**, with higher weekend performance.

 - **Revenue Distribution:** Business hotels contribute **~62%** of revenue, while luxury hotels contribute **~38%.**

 - **Platform Insights:** Direct offline bookings yield one of the highest realization percentages.

 - **Property Insights:** Certain hotels in Delhi and Mumbai consistently outperform others in ADR and realization %.
_________________________________________________________________________________________________________________
### Future Enhancements

 - Automate data refresh with **SQL + Power BI Service**.

 - Incorporate forecasting models for occupancy and revenue prediction.

 - Add customer segmentation analysis for deeper insights.

 - Expand analysis by integrating competitor and market trend data.

_________________________________________________________________________________________________________________

### Acknowledgments

 - **Data Simulation:** AtliQ Hospitality case dataset.

 - **Inspiration:** Real-world business intelligence projects in the hospitality industry.
