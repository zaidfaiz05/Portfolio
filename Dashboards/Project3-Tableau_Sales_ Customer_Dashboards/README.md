# Sales Performance- Dashboard

### Dashboard Link : https://public.tableau.com/app/profile/zaid.faiz/viz/Book1_17333879611040/Salesdashboard

## Problem Statement

This customer dashboard provides an overview of customer data, trends, and behaviors. It aids marketing teams and management in understanding customer segments and enhancing customer satisfaction.

### Data Source
- https://www.datawithbaraa.com/wp-content/uploads/2024/06/Sales-Dashboard-Materials.zip

### Key Visualizations

1) KPI Overview:
Summarizes the total number of customers, total sales per customer, and the total number of orders for the current and previous years.

2) Customer Trends:
- Displays monthly data for each KPI for the current and previous years.

- Highlights the months with the highest and lowest sales for easy recognition.

3) Shows the distribution of customers based on the number of orders, providing insights into customer behavior, loyalty, and engagement.

4) Top 10 Customers By Profit
- Lists the top 10 customers who generated the highest profits for the company.

- Includes additional information such as rank, number of orders, current sales, current profit, and the last order date.

### Calculated Fields

Min/Max Sales:

            IF SUM([CY Sales]) = WINDOW_MAX(SUM([CY Sales]))
            THEN SUM([CY Sales])
            ELSEIF  SUM([CY Sales]) = WINDOW_MIN(SUM([CY Sales]))
            THEN SUM([CY Sales])
            END

KPI Sales Avg: 

            IF SUM([CY Sales])> WINDOW_AVG(SUM([CY Sales]))
            THEN 'above'
            ELSE 'below'
            END

KPI CY Less PY:

            IF  SUM([CY Sales])<SUM([PY Sales]) THEN '⬤'
            ELSE ''
            END

Percentage Diff Sales:

            (SUM([CY Sales])-SUM([PY Sales]))/SUM([CY Sales])


### Snapshot of Dashboard

<img width="1202" alt="Image" src="https://github.com/user-attachments/assets/1f05cdc9-6f44-418e-9857-a20a5cc3b95b" />
 
<img width="1200" alt="Image" src="https://github.com/user-attachments/assets/f31776f6-04e7-4c64-ad0b-6dabccd1ccbd" />

### Insights

1. The dashboard allows users to view historical data by selecting any desired year.

2. It provides easy navigation between different dashboards.

3. Charts and graphs are interactive, enabling users to filter data directly through the visual elements.

4. Interactive filters allow users to filter data by product information (such as category and subcategory) and location information (such as region, state, and city).
