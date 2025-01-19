# Car Sales- Dashboard

### Dashboard Link : https://app.powerbi.com/links/RYgiDZRqvx?ctid=6ac7a1f4-5fb1-4153-bb4f-12d2020a1f7d&pbi_source=linkShare

## Problem Statement

The goal is to provide a centralized and interactive dashboard for tracking business performance across regions and products that enables stakeholders to derive meaningful insights quickly.

### Data Source
- Dataset: Sales data for a fictional company.
- Sources:

    a) CSV files with sales, product, and customer data.

    b) Some data required transformation and aggregation for analysis.

### Key Visualizations

1) Sales Performance Overview:
Line chart showing total revenue and trends over time.

2) Region-wise Analysis:
Map visualization to identify revenue contributions by region.

3) Product Category Insights:
DOnut charts displaying product-level performance.

4) Matrix       :
Metrics such as Total Revenue, Average Order Value, and Total Orders.

5) Interactive Slicers:
Filters for specific product category

### DAX Queries Used
            Avg Price = SUM(car_data[Price ($)])/COUNT(car_data[Car_id])

            MTD Avg Price = TOTALMTD([Avg Price],'Calendar Table'[Date])

            PYTD Avg Price = CALCULATE([Avg Price], SAMEPERIODLASTYEAR('Calendar Table'[Date]))

            Avg Price Color = IF([Avg Price Difference]>0,"Green","Red")
            
            Max point on Area Chart = if(MAXX(ALLSELECTED('Calendar Table'[Week]),[Total Sales])= [Total Sales], 
                                        MAXX(ALLSELECTED('Calendar Table'[Week]),[Total Sales]), BLANK())

### Snapshot of Dashboard
![overview-jpg](https://github.com/user-attachments/assets/d189c920-bb90-4d07-b3ad-0b0e62f3b2ab)
 
![Details-jpg](https://github.com/user-attachments/assets/640fddb1-068f-47bc-891c-7d27437dd551)

### Insights

1) Identified top-performing regions contributing the majority of the revenue.
2) Highlighted seasonal trends with significant revenue spikes during certain months.
3) Determined that specific product categories drive higher profit margins.
4) Year-over-year analysis revealed growth in revenue but a decline in profit margins, suggesting cost optimization is necessary.

### Conclusion
The dashboard provides a holistic view of sales performance, enabling the business to:

1) Make data-driven decisions for improving revenue.
2) Focus marketing efforts on high-performing regions and product categories.
Identify areas for cost-saving and profitability enhancement.