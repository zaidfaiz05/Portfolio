# Car Sales- Dashboard

### Dashboard Link : https://app.powerbi.com/groups/me/reports/4677aa36-c3fa-42a4-ad8e-4e6b7a835da5/ReportSection?experience=power-bi

## Problem Statement

The primary objective was to create a comprehensive dashboard that allows users to:

- Monitor patient visit trends over time.
- Assess average wait times and patient satisfaction scores.
- Differentiate between administrative and non-administrative appointments.
- Analyze patient demographics, including age groups and race.

The challenge was to present this information in an intuitive and interactive format, facilitating easy exploration and analysis for healthcare stakeholders.

### Data Source
- Dataset: Sales data for a fictional hospital.
- Sources:

    a) CSV files with healthcare data encompassing patient visits, wait times, satisfaction scores, appointment types, age groups, and racial demographics.

    b) Some data required transformation and aggregation for analysis.

### Key Visualizations

Patient Visits Over Time:
Line chart displaying the total number of patient visits on a monthly basis.
Helps identify trends and seasonal patterns in patient influx.

Average Wait Time Analysis: 
Bar chart illustrating average wait times for patients across different departments or services.
Assists in pinpointing areas requiring process improvements.

Patient Satisfaction Scores: 
Gauge chart representing average patient satisfaction ratings.
Provides a quick overview of patient experience quality.

Appointment Types Distribution:
Pie chart differentiating between administrative and non-administrative appointments.
Offers insights into the nature of patient engagements.

Patient Demographics:
Stacked bar chart showing patient visits segmented by age group and race.
Enables analysis of the diversity in patient population.


### DAX Queries Used
Purpose: Calculate the total number of patient visits.

        Total Patient Visits = COUNTROWS(PatientVisits)


Purpose: Compute the average wait time for patients.

        Average Wait Time = AVERAGE(PatientVisits[WaitTime])

Determine the average satisfaction score from patient feedback.

        Average Satisfaction Score = AVERAGE(PatientFeedback[SatisfactionScore])

Count the number of appointments by type (Administrative vs. Non-Administrative).

        Appointment Type Count = COUNTROWS(Appointments)

Purpose: Analyze patient visits segmented by age group and race.

        Patient Visits by Age Group = CALCULATE(
    COUNTROWS(PatientVisits),
    FILTER(PatientDemographics, PatientDemographics[AgeGroup] = "Selected Age Group")

### Snapshot of Dashboard
![patient_er_visit_report](https://github.com/user-attachments/assets/bfc10233-087d-464e-8d68-ae6c68c199aa)

### Insights

1) Patient Visit Trends: The dashboard revealed fluctuations in patient visits, with noticeable peaks during certain months, indicating potential seasonal factors affecting healthcare demand.

2) Wait Time Analysis: Certain departments exhibited longer average wait times, suggesting areas where operational efficiencies could be improved to enhance patient experience.

3) Satisfaction Scores: Overall patient satisfaction was high, though specific services showed lower scores, highlighting opportunities for targeted quality improvements.

4) Appointment Types: A significant proportion of appointments were administrative, indicating a potential area to streamline processes and allocate more resources to direct patient care.

5) Demographic Distribution: The patient population was diverse across age groups and races, emphasizing the need for culturally competent care and tailored health services.

### Conclusion
The dashboard provided a comprehensive view of hospital and clinic operations, enabling healthcare administrators to:

- Monitor and respond to patient visit trends effectively.
- Identify and address departments with longer wait times.
- Enhance patient satisfaction through targeted improvements.
- Optimize appointment scheduling and resource allocation.
- Deliver personalized care by understanding patient demographics.

This project demonstrates the ability to transform complex healthcare data into actionable insights using Power BI, facilitating data-driven decision-making in a clinical setting.