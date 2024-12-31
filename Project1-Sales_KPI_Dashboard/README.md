# Car Sales- Dashboard

### Dashboard Link : https://app.powerbi.com/links/RYgiDZRqvx?ctid=6ac7a1f4-5fb1-4153-bb4f-12d2020a1f7d&pbi_source=linkShare

## Problem Statement

The goal was to provide a centralized and interactive dashboard for tracking business performance across regions and products. The challenge was to consolidate data, clean it, and create a user-friendly interface that enables stakeholders to derive meaningful insights quickly.

### Data Source
- Dataset: Sales data for a fictional company.

### Key Visualizations

1) Sales Performance Overview:
Line chart showing Total Sales and trends over time.

2) Region-wise Analysis:
Map visualization to identify Total Orders contributions by region.

3) Product Category Insights:
Pie charts charts displaying product-level performance.

4) KPI Cards:
Metrics such as Total Sales, Sales Difference, Year on Year Sales Growth and Total Orders.

5) Interactive Slicers:
Filters for overview, details, and product categories.

### DAX Queries Used
            Avg Price Color = IF([Avg Price Difference]>0,"Green","Red") 

            YoY Avg Price Growth = [Avg Price Difference]/[PYTD Avg Price]

            PYTD Avg Price = CALCULATE([Avg Price], SAMEPERIODLASTYEAR('Calendar Table'[Date]))

            Max point on Area Chart = IF(MAXX(ALLSELECTED('Calendar Table'[Week]),[Total Sales])= [Total Sales],   
            MAXX(ALLSELECTED('Calender Table'[WEEK]),[Total Sales]), BLANK())

            Calendar table = CALENDAR(MIN(car_data[Date]), MAX(car_data[Date]))
            Month = FORMAT('Calendar Table'[Date],"MMMM")
            Week = WEEKNUM('Calendar Table'[Date])
            Year = YEAR('Calendar Table'[Date])
                
            

            

### Snapshot of Dashboard
![overview-jpg](https://github.com/user-attachments/assets/d189c920-bb90-4d07-b3ad-0b0e62f3b2ab)
 
![Details-jpg](https://github.com/user-attachments/assets/640fddb1-068f-47bc-891c-7d27437dd551)

### Insights

1) Identified top-performing regions contributing the majority of the revenue.
2) Highlighted the highest sales.
3) Determined that specific product categories drive higher profit margins.
4) Year-over-year analysis revealed growth in revenue but a decline in profit margins, suggesting cost optimization is necessary.

### Conclusion

The dashboard provides a holistic view of sales performance, enabling the business to:
1) Make data-driven decisions for improving revenue.
2) Focus marketing efforts on high-performing regions and product categories.
3) Identify areas for cost-saving and profitability enhancement.
