# Car Sales- Dashboard

### Dashboard Link : https://app.powerbi.com/links/RYgiDZRqvx?ctid=6ac7a1f4-5fb1-4153-bb4f-12d2020a1f7d&pbi_source=linkShare

## Problem Statement

The goal was to provide a centralized and interactive dashboard for tracking business performance across regions and products. The challenge was to consolidate data from multiple sources, clean it, and create a user-friendly interface that enables stakeholders to derive meaningful insights quickly.

### Data Source
- Dataset: Sales data for a fictional company.
- Sources:

    a) CSV files with sales, product, and customer data.

    b) Some data required transformation and aggregation for analysis.

### Key Visualizations

1) Sales Performance Overview:
Bar and line charts showing total revenue and trends over time.

2) Region-wise Analysis:
Map visualization to identify revenue contributions by region.

3) Product Category Insights:
Stacked bar charts displaying product-level performance.

4) KPI Cards:
Metrics such as Total Revenue, Average Order Value, and Total Orders.

5) Interactive Slicers:
Filters for time, region, and product categories.

### DAX Queries Used
            Total Revenue = SUM(Sales[Revenue]) 

            Profit Margin = DIVIDE([Total Profit], [Total Revenue], 0)  
            
            YoY Revenue Growth = DIVIDE([Total Revenue] - CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Dates[Date])),  
            CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Dates[Date])), 0)  

            Top Products = TOPN(10, Sales, Sales[Revenue], DESC)  

### Snapshot of Dashboard
![overview-jpg](https://github.com/user-attachments/assets/d189c920-bb90-4d07-b3ad-0b0e62f3b2ab)
 
![Details-jpg](https://github.com/user-attachments/assets/640fddb1-068f-47bc-891c-7d27437dd551)

### Insights

1) Identified top-performing regions contributing the majority of the revenue.
2) Highlighted seasonal trends with significant revenue spikes during certain months.
3) Determined that specific product categories drive higher profit margins.
4) Year-over-year analysis revealed growth in revenue but a decline in profit margins, suggesting cost optimization is necessary.

### Conclusion
1) The dashboard provides a holistic view of sales performance, enabling the business to:
2) Make data-driven decisions for improving revenue.
3) Focus marketing efforts on high-performing regions and product categories.
Identify areas for cost-saving and profitability enhancement.