# HR Analytics Dashboard: Documentation

## Contents

1. [Data Dictionary](#1-data-dictionary)
2. [Data Preparation](#2-data-preparation)
3. [Data Model & DAX](#3-data-model--dax)
4. [Dashboard Guide](#4-dashboard-guide)
5. [Insights & Recommendations](#5-insights--recommendations)
6. [Known Issues & Roadmap](#6-known-issues--roadmap)

---

## 1. Data Dictionary

Table: **`HR data`** — 1,470 rows × 26 columns, one row per employee. No missing values and no duplicate Employee Numbers.

> Scales marked *(assumed)* are not defined in the file. Confirm them against the original data source before publishing.

### Identifiers & Target

| Column | Type | Values / Range | Description |
|---|---|---|---|
| Employee Number | Text | `EMP_xxx`, unique | Anonymised employee ID |
| Attrition | Text | `Yes` (237) / `No` (1,233) | Whether the employee left the company |
| Attrition Count | Whole number | 0 / 1 | **Calculated.** 1 if Attrition = "Yes", else 0. Makes attrition summable |
| Employee Count | Whole number | Always 1 | Helper column, one per employee. Used for headcount |

### Demographics

| Column | Type | Values / Range | Description |
|---|---|---|---|
| Age | Whole number | 18–60 (mean 36.9) | Age in years |
| Age Group | Text | Under 25, 25 - 34, 35 - 44, 45 - 54, Over 55 | Age band (note the spaces around the hyphen) |
| Sorted Age | Whole number | 1–5 | **Calculated.** Intended sort order for Age Group. **Currently buggy**, see [Known Issues](#6-known-issues--roadmap) |
| Gender | Text | Male (882), Female (588) | Gender |
| Marital Status | Text | Single, Married, Divorced | Marital status |
| Education | Text | High School, Associates Degree, Bachelor's Degree, Master's Degree, Doctoral Degree | Highest education level |
| Education Field | Text | Life Sciences, Medical, Marketing, Technical Degree, Human Resources, Other | Field of study |

### Job & Organisation

| Column | Type | Values / Range | Description |
|---|---|---|---|
| Department | Text | R&D, Sales, HR | Department |
| Job Role | Text | 9 roles, e.g. Sales Executive, Research Scientist, Laboratory Technician, Manager | Job title |
| Job Level | Whole number | 1–5 | Seniority level (1 = most junior) *(assumed)* |
| Business Travel | Text | Non-Travel, Travel_Rarely, Travel_Frequently | Travel frequency |
| Over Time | Text | Yes / No | Whether the employee works overtime |
| Distance From Home | Whole number | 1–29 | Commute distance (unit not specified) |

### Compensation

| Column | Type | Values / Range | Description |
|---|---|---|---|
| Monthly Income | Whole number | 1,009–19,999 (mean 6,503) | Monthly pay (currency not specified) |

### Satisfaction Scores (1–4)

*(assumed: 1 = Low, 4 = High)*

| Column | Description |
|---|---|
| Job Satisfaction | Satisfaction with the job |
| Environment Satisfaction | Satisfaction with the work environment |
| Work Life Balance | Self-rated work–life balance |

### Tenure

| Column | Type | Range | Description |
|---|---|---|---|
| Total Working Years | Whole number | 0–40 | Total career length |
| Years At Company | Whole number | 0–40 | Tenure at this company |
| Years In Current Role | Whole number | 0–18 | Time in present role |
| Years Since Last Promotion | Whole number | 0–15 | Time since last promotion |
| Years With Curr Manager | Whole number | 0–17 | Time with current manager |

---

## 2. Data Preparation

### Source

| Item | Detail |
|---|---|
| File | `Company Data.xlsx` |
| Sheet | `HR data` |
| Connector | Excel workbook |
| Load mode | Import |
| Result | One table, `HR data`, 1,470 rows × 26 columns |

### Transformation Steps

The query applies these steps in order:

| # | Step | Purpose |
|---|---|---|
| 1 | Source: `Excel.Workbook(...)` | Connect to the workbook |
| 2 | Navigate to sheet `HR data` | Select the data sheet |
| 3 | Promote headers | Use first row as column names |
| 4 | Change type | Text for Employee Number, Attrition, Business Travel, Age Group, Department, Education Field, Gender, Job Role, Marital Status, Over Time, Education; whole number for all numeric columns |
| 5 | Sort by Employee Number (ascending) | Consistent row order |
| 6 | Add conditional column **Attrition Count** | `if [Attrition] = "Yes" then 1 else 0` |
| 7 | Reorder columns | Place Attrition Count next to Attrition |
| 8 | Change type (Attrition Count) | Whole number |
| 9 | Add conditional column **Sorted Age** | Numeric sort key for Age Group (see issue below) |
| 10 | Change type (Sorted Age) | Whole number |

#### Why the helper columns exist

- **Attrition Count (0/1)** turns a Yes/No text field into something you can `SUM`, which makes the attrition rate a simple division.
- **Sorted Age (1–5)** is meant to be used with *Sort by column* so age groups display in logical order instead of alphabetically.

> **Known issue:** the Sorted Age logic compares against `"25-34"`, `"35-44"`, `"45-54"` (no spaces) but the data contains `"25 - 34"` etc. (spaces around the hyphen). Those three groups all fall through to the `else 5` branch. Fix in [Known Issues & Roadmap](#1-sorted-age-column-maps-three-age-groups-to-the-wrong-value).

### Data Quality Checks

| Check | Result |
|---|---|
| Missing values | 0 across all columns |
| Duplicate Employee Numbers | 0 |
| Employee Count values | Always 1 (no information beyond headcount) |
| Age vs Age Group consistency | All ages fall inside their stated band |
| Years At Company ≤ Total Working Years | Holds for all rows |
| Attrition class balance | 16.1% Yes / 83.9% No (imbalanced; relevant for any future modelling) |

### Re-pointing the Data Source

The original path was a local Downloads folder, so the file will not refresh on another machine.

1. **Home > Transform data > Data source settings**
2. Select the source > **Change Source...**
3. Browse to your copy of `Company Data.xlsx`


### Recommended Improvement

Store the source path as a Power Query **parameter** (`FilePath`) so it can be changed in one place.

---

## 3. Data Model & DAX

### Model

A **single flat table** (`HR data`) with no relationships. This suits a dataset with one row per employee and keeps every measure easy to follow.

```
┌──────────────────────────────┐
│          HR data             │
│  1,470 rows · 26 columns     │
│  6 measures                  │
└──────────────────────────────┘
```

### DAX Measures

All measures live in the `HR data` table.

#### Total_Employees
```dax
Total_Employees = COUNT('HR data'[Employee Count])
```
Headcount. Because Employee Count is always 1 this equals the number of rows (1,470) in the current filter context.

#### Total_Attrition
```dax
Total_Attrition = SUM('HR data'[Attrition Count])
```
Number of employees who left (237 unfiltered).

#### Attrition_Rate
```dax
Attrition_Rate = DIVIDE([Total_Attrition], [Total_Employees])
```
Share of employees who left (16.1% unfiltered). `DIVIDE` returns blank instead of an error when the denominator is zero, which is what you want when slicers filter down to empty groups.

#### Active_Employees
```dax
Active_Employees = [Total_Employees] - [Total_Attrition]
```
Employees still with the company (1,233 unfiltered).

#### Avg_Age
```dax
Avg_Age = AVERAGE('HR data'[Age])
```

#### Average_Salary
```dax
Average_Salary = AVERAGE('HR data'[Monthly Income])
```
Average monthly income.

> **Note:** the dashboard's *Avg Age* and *Avg Monthly Inc.* cards use the built-in **Average** aggregation of the Age and Monthly Income columns rather than these two measures. The numbers are identical. Switching the cards to the measures is a cleanup option (see roadmap).

### Measure Dependency

```
Total_Employees ─┐
                 ├─> Attrition_Rate
Total_Attrition ─┤
                 └─> Active_Employees (Total_Employees − Total_Attrition)
```

### Calculated Columns (created in Power Query)

| Column | Logic |
|---|---|
| Attrition Count | 1 if Attrition = "Yes", else 0 |
| Sorted Age | Numeric sort key for Age Group (has a known bug) |

### Design Notes

- Rates are computed as **measures**, not columns, so they re-aggregate correctly at any grouping (by role, department, etc.) instead of averaging averages.
- Measures reference other measures, so a change to `Total_Employees` flows through to the rate and active count.

---

## 4. Dashboard Guide

A single 16:9 page (1280 × 720) named **DASHBOARD**, titled *"Interactive Dashboard for HR Data"*.

### Layout

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

### KPI Cards

| Card | Source | Unfiltered value |
|---|---|---|
| Total Employees | `[Total_Employees]` | 1,470 |
| Total Attrition | `[Total_Attrition]` | 237 |
| Attrition Rate | `[Attrition_Rate]` | 16.1% |
| Active Employees | `[Active_Employees]` | 1,233 |
| Avg Age | Average of `Age` | 36.9 |
| Avg Monthly Inc. | Average of `Monthly Income` | 6,503 |

Each card carries a custom icon image.

### Visuals

| # | Visual | Type | Fields | Question it answers |
|---|---|---|---|---|
| 1 | Employees by age group | Clustered column | Axis: Age Group · Legend: Gender · Value: Employee Count | What does the workforce look like by age and gender? |
| 2 | Attrition across ages | Area chart | Axis: Age · Value: Count of Attrition | How does the age profile look? *(see Known Issues: counts all employees)* |
| 3 | Department split | Donut | Legend: Department · Value: Count of Attrition | Where is headcount concentrated? *(see Known Issues)* |
| 4 | Attrition rate by job role | Clustered bar | Axis: Job Role · Value: `[Attrition_Rate]` | Which roles lose the highest share of people? |
| 5 | Job role × job satisfaction | Matrix | Rows: Job Role · Columns: Job Satisfaction · Value: `[Total_Employees]` | How is satisfaction distributed within each role? |
| 6 | Attrition by education field | Funnel | Category: Education Field · Value: `[Total_Attrition]` | Which fields of study do leavers come from (by count)? |

### Slicers

| Slicer | Field |
|---|---|
| Education | Education |
| Gender | Gender |
| Marital Status | Marital Status |
| Business Travel | Business Travel |

The **Clear all slicers** button resets every slicer on the page in one click.

### How to Use It

1. **Start with the KPI row** for the overall picture (16.1% attrition).
2. **Slice a segment**, for example Marital Status = Single, and watch every KPI and chart update.
3. **Click a chart element** (a job role, a department slice) to cross-filter the rest of the page.
4. **Compare rate vs count.** The job role bar chart shows *rate*, the funnel shows *count*. A big group can have a high count and a low rate (Life Sciences), while a small group can show the opposite (Human Resources field).
5. Hit **Clear all slicers** to reset.

### Design Notes

- Theme: Power BI built-in base theme (CY26SU07).
- Slicers on the left and top keep the analysis area uncluttered.
- Rates are used for comparisons across groups of different sizes; counts are used to show scale.

---

## 5. Insights & Recommendations

Overall: **237 of 1,470 employees left, an attrition rate of 16.1%.**

> All rates below are calculated from the data in the model (`n` = group size). They show **association, not causation**, and some groups are small. Treat n < 100 with caution.

### 1. Workload and travel

| Segment | n | Attrition rate |
|---|---|---|
| Works overtime | 416 | **30.5%** |
| No overtime | 1,054 | 10.4% |
| Travels frequently | 277 | **24.9%** |
| Travels rarely | 1,043 | 15.0% |
| Non-travel | 150 | 8.0% |

Overtime employees leave at nearly **3x** the rate of others, the biggest single gap in the dataset.

### 2. Age and life stage

| Age group | n | Rate |
|---|---|---|
| Under 25 | 97 | **39.2%** |
| 25–34 | 554 | 20.2% |
| 35–44 | 505 | 10.1% |
| 45–54 | 245 | 10.2% |
| Over 55 | 69 | 15.9% |

| Marital status | n | Rate |
|---|---|---|
| Single | 470 | **25.5%** |
| Married | 673 | 12.5% |
| Divorced | 327 | 10.1% |

### 3. Role and department

| Job role | n | Rate |
|---|---|---|
| Sales Representative | 83 | **39.8%** |
| Laboratory Technician | 259 | 23.9% |
| Human Resources | 52 | 23.1% |
| Sales Executive | 326 | 17.5% |
| Research Scientist | 292 | 16.1% |
| Healthcare Representative | 131 | 6.9% |
| Manufacturing Director | 145 | 6.9% |
| Manager | 102 | 4.9% |
| Research Director | 80 | 2.5% |

By department: Sales 20.6% · HR 19.0% (n = 63) · R&D 13.8%. Entry-level roles lose the most people; senior and leadership roles are the most stable.

### 4. Pay, level and satisfaction

| Metric | Left | Stayed |
|---|---|---|
| Avg monthly income | 4,787 | 6,833 |
| Avg age | 33.6 | 37.6 |
| Avg years at company | 5.1 | 7.4 |
| Avg distance from home | 10.6 | 8.9 |

- **Job Level 1** attrition is 26.3%, versus 4.7%–14.7% at higher levels.
- **Job Satisfaction 1** (lowest) has 22.8% attrition vs 11.3% at level 4.
- **Work Life Balance 1** has 31.2% attrition, but n = 80, so interpret cautiously.

### 5. Education

Education level shows only a weak pattern (High School 18.2% down to Doctoral 10.4%, n = 48). By field, Human Resources (25.9%, n = 27), Technical Degree (24.2%) and Marketing (22.0%) are highest. Life Sciences has the most leavers in absolute count (89) but a mid-range rate (14.7%).

### Recommendations

1. **Review overtime and workload**, particularly for Sales and Lab roles, where retention risk is concentrated.
2. **Target early-career retention** (under 25 and Job Level 1) with onboarding, mentoring and clear progression paths.
3. **Investigate Sales Representative turnover**: check pay, targets and management. At 39.8% it is a prime candidate for an exit-interview programme.
4. **Look at travel policies** for frequent travellers: caps, rotation, or compensation.
5. **Benchmark pay for junior roles**, since leavers earn about 30% less on average (4,787 vs 6,833).
6. **Track satisfaction and work-life balance** as early-warning indicators.

### Suggested Next Steps

- Add statistical testing or a simple logistic regression to see which factors hold up after controlling for each other (overtime, level and income are likely correlated).
- Add trend data (attrition by period) if dates become available.

---

## 6. Known Issues & Roadmap

Documenting limitations openly: these were found while reviewing the file, with the fix for each.

### Known Issues

#### 1. Sorted Age column maps three age groups to the wrong value

**What happens:** The Power Query conditional column checks for `"25-34"`, `"35-44"`, `"45-54"`, but the data contains `"25 - 34"`, `"35 - 44"`, `"45 - 54"` (spaces around the hyphen). Those three groups fall to the `else 5` branch.

| Age Group | Intended | Actual |
|---|---|---|
| Under 25 | 1 | 1 |
| 25 - 34 | 2 | **5** |
| 35 - 44 | 3 | **5** |
| 45 - 54 | 4 | **5** |
| Over 55 | 5 | 5 |

**Fix:** match the exact labels in the *Added Conditional Column1* step:

```m
Table.AddColumn(#"Changed Type1", "Sorted Age", each
    if [Age Group] = "Under 25" then 1
    else if [Age Group] = "25 - 34" then 2
    else if [Age Group] = "35 - 44" then 3
    else if [Age Group] = "45 - 54" then 4
    else 5)
```

Then use **Column tools > Sort by column > Sorted Age** on Age Group.

#### 2. "Count of Attrition" visuals count all employees

**What happens:** The area chart (by Age) and the donut (by Department) use `Count of Attrition`, which counts every non-blank value in the Attrition column. Because the column holds "Yes" and "No", this is **headcount**, not attrition.

**Fix:** replace the value field with the `[Total_Attrition]` measure (leavers) or `[Attrition_Rate]` (rate), and retitle accordingly. If headcount is the intended message, rename the visual to "Employees by Age/Department".

#### 3. Hard-coded local file path

The data source points to a local Downloads folder, so refresh fails on other machines. **Fix:** use a Power Query parameter or relative source (see [Data Preparation](#re-pointing-the-data-source)).

#### 4. Two measures are defined but not used

`Avg_Age` and `Average_Salary` exist, but the cards use implicit column averages. Cosmetic; switch the cards to the measures for consistency.

#### 5. Single page, no visual titles on some charts

Adding clear titles and axis labels would make the visuals self-explanatory for new viewers.

### Limitations

- Snapshot data with **no date field**, so no attrition trend over time.
- Attrition is binary with no leaving date or reason.
- Correlation only; factors such as overtime, level and income overlap.
- Currency and distance units are not specified in the source.

### Roadmap

- [ ] Fix issues 1 and 2 above
- [ ] Add a **Key Insights** text panel or second page with the findings in `INSIGHTS.md`
- [ ] Add a decomposition tree for attrition drivers
- [ ] Add income bands and tenure bands as extra dimensions
- [ ] Add tooltips showing rate and headcount together
- [ ] Add a Power Query parameter for the file path
- [ ] Publish to Power BI Service and link the live report in the README
- [ ] Optional: logistic regression in Python to rank attrition drivers
