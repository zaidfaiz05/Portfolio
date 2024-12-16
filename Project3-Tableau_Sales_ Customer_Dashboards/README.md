# Sales & Customer Dashboard

### Dashboard Link : https://public.tableau.com/views/Book1_17333879611040/Salesdashboard?:language=en-GB&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

## Project overview

This project demonstrates the end-to-end development of an interactive Tableau dashboard, showcasing the process from understanding user requirements to delivering a polished, user-friendly data visualization tool.

## Problem Statement

The objective was to create a comprehensive dashboard that enables stakeholders to monitor key performance indicators (KPIs), analyze sales trends, and compare subcategory performances. The challenge involved transforming raw data into actionable insights through effective visualization techniques.

### Data Source
- Dataset: Sales data for a fictional company.

### Key Visualizations

KPI Sales Chart:
Displays current year sales figures alongside sparklines to illustrate sales trends over time.

Subcategory Comparison Chart:
Bar-in-bar charts comparing current and previous year sales across different product subcategories.

Sales Trend Line Chart:
Line chart depicting monthly sales trends, highlighting year-over-year comparisons.

Interactive Filters:
Dynamic filters allowing users to select specific years, products, and locations for tailored data analysis.

### Calculated Fields and Parameters
Current Year Sales:

        IF YEAR([Order Date]) = YEAR(TODAY()) THEN [Sales] ELSE 0 END

Previous Year Sales:

        IF YEAR([Order Date]) = YEAR(TODAY()) - 1 THEN [Sales] ELSE 0 END

Sales Difference:

        [Current Year Sales] - [Previous Year Sales]

Percentage Difference:

        ([Sales Difference] / [Previous Year Sales]) * 100

### Snapshot of Dashboard
![jpg-Screenshot 2024-12-16 at 3 27 42 PM](https://github.com/user-attachments/assets/14125cd1-3d5b-4041-894a-431c914555b5)

![Screenshot 2024-12-16 at 3 28 43 PM](https://github.com/user-attachments/assets/17714982-13fc-433f-9b72-d5b86496c1b8)


### Insights

Sales Performance: The KPI chart provides a quick overview of the current year's sales, with sparklines indicating monthly performance trends.

Subcategory Analysis: The comparison chart highlights which subcategories have experienced growth or decline compared to the previous year, aiding in strategic decision-making.

Trend Analysis: The line chart offers a clear view of sales trends over time, facilitating the identification of seasonal patterns and anomalies.

### Conclusion
This project exemplifies the process of building a Tableau dashboard from the ground up, emphasizing the importance of understanding user requirements, preparing data sources, designing intuitive visualizations, and implementing interactive features. The final dashboard serves as a valuable tool for stakeholders to explore data, derive insights, and make informed decisions.
