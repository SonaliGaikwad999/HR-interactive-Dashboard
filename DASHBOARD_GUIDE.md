# Dashboard Guide

A single 16:9 page (1280 × 720) named **DASHBOARD**, titled *"Interactive Dashboard for HR Data"*.

## Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ Title                        │  Education slicer (top bar)            │
├───────────────────────────────────────────────────────────────────────┤
│              KPI cards: Employees · Attrition · Rate · Active · Age · Pay │
├──────────┬──────────────────┬───────────────────┬─────────────────────┤
│ Gender   │ Age Group×Gender │ Attrition by Age  │ Department donut    │
│ slicer   │ (clustered col.) │ (area)            │                     │
│ Marital  ├──────────────────┼───────────────────┼─────────────────────┤
│ slicer   │ Attrition rate   │ Job Role ×        │ Attrition by        │
│ Business │ by Job Role (bar)│ Job Satisfaction  │ Education Field     │
│ Travel   │                  │ (matrix)          │ (funnel)            │
│ [Clear all slicers]         │                   │                     │
└──────────┴──────────────────┴───────────────────┴─────────────────────┘
```

## KPI Cards

| Card | Source | Unfiltered value |
|---|---|---|
| Total Employees | `[Total_Employees]` | 1,470 |
| Total Attrition | `[Total_Attrition]` | 237 |
| Attrition Rate | `[Attrition_Rate]` | 16.1% |
| Active Employees | `[Active_Employees]` | 1,233 |
| Avg Age | Average of `Age` | 36.9 |
| Avg Monthly Inc. | Average of `Monthly Income` | 6,503 |

Each card carries a custom icon image.

## Visuals

| # | Visual | Type | Fields | Question it answers |
|---|---|---|---|---|
| 1 | Employees by age group | Clustered column | Axis: Age Group · Legend: Gender · Value: Employee Count | What does the workforce look like by age and gender? |
| 2 | Attrition across ages | Area chart | Axis: Age · Value: Count of Attrition | How does the age profile look? *(see Known Issues: counts all employees)* |
| 3 | Department split | Donut | Legend: Department · Value: Count of Attrition | Where is headcount concentrated? *(see Known Issues)* |
| 4 | Attrition rate by job role | Clustered bar | Axis: Job Role · Value: `[Attrition_Rate]` | Which roles lose the highest share of people? |
| 5 | Job role × job satisfaction | Matrix | Rows: Job Role · Columns: Job Satisfaction · Value: `[Total_Employees]` | How is satisfaction distributed within each role? |
| 6 | Attrition by education field | Funnel | Category: Education Field · Value: `[Total_Attrition]` | Which fields of study do leavers come from (by count)? |

## Slicers

| Slicer | Field |
|---|---|
| Education | Education |
| Gender | Gender |
| Marital Status | Marital Status |
| Business Travel | Business Travel |

The **Clear all slicers** button resets every slicer on the page in one click.

## How to Use It

1. **Start with the KPI row** for the overall picture (16.1% attrition).
2. **Slice a segment**, for example Marital Status = Single, and watch every KPI and chart update.
3. **Click a chart element** (a job role, a department slice) to cross-filter the rest of the page.
4. **Compare rate vs count.** The job role bar chart shows *rate*, the funnel shows *count*. A big group can have a high count and a low rate (Life Sciences), while a small group can show the opposite (Human Resources field).
5. Hit **Clear all slicers** to reset.

## Design Notes

- Theme: Power BI built-in base theme (CY26SU07).
- Slicers on the left and top keep the analysis area uncluttered.
- Rates are used for comparisons across groups of different sizes; counts are used to show scale.
