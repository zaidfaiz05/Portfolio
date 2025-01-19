# Patients Admission- Dashboard

### Dashboard Link : https://app.powerbi.com/groups/me/reports/4677aa36-c3fa-42a4-ad8e-4e6b7a835da5/ReportSection?experience=power-bi

## Problem Statement

The primary objective is to create a comprehensive dashboard that allows users to:

- Monitor patient visit trends over time.
- Assess average wait times and patient satisfaction scores.
- Differentiate between administrative and non-administrative appointments.
- Analyze patient demographics, including age groups and race.

The challenge is to present this information in an intuitive and interactive format, facilitating easy exploration and analysis for healthcare stakeholders.

### Data Source
- Dataset: Sales data for a fictional hospital.
- Sources:

    a) CSV files with healthcare data encompassing patient visits, wait times, satisfaction scores, appointment types, age groups, and racial demographics.

    b) Some data required transformation and aggregation for analysis.

### Key Visualizations

Patient Visits Over Time:
Line chart displaying the total number of patient visits on a monthly basis. Helps identify trends and seasonal patterns in patient influx.

Score cards: 
Monitor average wait times and average satisfaction. Assists in pinpointing areas requiring process improvements.

Patient Demographics:
Heatmap showing patient visits segmented by age group and race. Enables analysis of the diversity in patient population.


### DAX Queries Used
Purpose: Calculate administrative appointment vs non- administrative appointment

        % Administrative Schedule = 
    DIVIDE(
        COUNTROWS(
            FILTER(
                'Patients dataset',
                'Patients dataset'[patient_admin_flag]=TRUE())
    ),
    [Total Patients]
    )


Purpose: Determine highest total patient visit in a given year.

        Value Max Point (Year) = 
    VAR _PatientTable = 
        CALCULATETABLE(
            ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Year]),
            "@Total_Patients",[Total Patients]
            ),
            ALLSELECTED()
        )
    VAR _MinValu = MINX(_PatientTable,[@Total_Patients])
    VAR _MaxValu = MAXX(_PatientTable,[@Total_Patients])
    VAR _TotalPatients = [Total Patients]
    RETURN
    SWITCH(
        TRUE(),
        _TotalPatients = _MinValu,[Total Patients],
        _TotalPatients = _MaxValu,[Total Patients]
    )

Determine the average satisfaction score from patient feedback.

        Average Satisfaction Score = 
    CALCULATE(
        AVERAGE('Patients dataset'[patient_sat_score]),
        'Patients dataset'[patient_sat_score]<>BLANK()
    )

Count the number of appointments by type (Administrative vs. Non-Administrative).

        Appointment Type Count = COUNTROWS(Appointments)

Purpose: Analyze patient visits segmented by age group and race.

       Heatmap Caption = 
    VAR _SelectedMeasure = 
        SELECTEDVALUE(Parameter[Parameter Order])
        RETURN
        IF(_SelectedMeasure=0,
        "The Darkest GREEN on the scale denotes LOW wait TIME on the Age-Group",
        "Patients are most SATISFIED when the SCALE shows the darkest GREEN on the Age-Group"
        )

### Snapshot of Dashboard
![patient_er_visit_report](https://github.com/user-attachments/assets/bfc10233-087d-464e-8d68-ae6c68c199aa)

### Insights

1) Patient Visit Trends: The dashboard revealed fluctuations in patient visits, with noticeable peaks during certain months, indicating potential seasonal factors affecting healthcare demand.

2) Wait Time Analysis: Age group and patient race do not determine the wait time.

4) Referral Types: A significant proportion of patients are not referred by any department.

### Conclusion
The dashboard provided a comprehensive view of hospital operations, enabling healthcare administrators to:

- Monitor and respond to patient visit trends effectively.
- Identify and address departments with longer wait times.
- Enhance patient satisfaction through targeted improvements.
- Optimize appointment scheduling and resource allocation.
- Deliver personalized care by understanding patient demographics.