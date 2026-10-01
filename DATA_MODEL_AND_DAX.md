# Data Model & DAX Measures

## Model

A **single flat table** (`HR data`) with no relationships. This suits a dataset with one row per employee and keeps every measure easy to follow.

```
┌──────────────────────────────┐
│          HR data             │
│  1,470 rows · 26 columns     │
│  6 measures                  │
└──────────────────────────────┘
```

## DAX Measures

All measures live in the `HR data` table.

### Total_Employees
```dax
Total_Employees = COUNT('HR data'[Employee Count])
```
Headcount. Because Employee Count is always 1 this equals the number of rows (1,470) in the current filter context.

### Total_Attrition
```dax
Total_Attrition = SUM('HR data'[Attrition Count])
```
Number of employees who left (237 unfiltered).

### Attrition_Rate
```dax
Attrition_Rate = DIVIDE([Total_Attrition], [Total_Employees])
```
Share of employees who left (16.1% unfiltered). `DIVIDE` returns blank instead of an error when the denominator is zero, which is what you want when slicers filter down to empty groups.

### Active_Employees
```dax
Active_Employees = [Total_Employees] - [Total_Attrition]
```
Employees still with the company (1,233 unfiltered).

### Avg_Age
```dax
Avg_Age = AVERAGE('HR data'[Age])
```

### Average_Salary
```dax
Average_Salary = AVERAGE('HR data'[Monthly Income])
```
Average monthly income.

> **Note:** the dashboard's *Avg Age* and *Avg Monthly Inc.* cards use the built-in **Average** aggregation of the Age and Monthly Income columns rather than these two measures. The numbers are identical. Switching the cards to the measures is a cleanup option (see roadmap).

## Measure Dependency

```
Total_Employees ─┐
                 ├─> Attrition_Rate
Total_Attrition ─┤
                 └─> Active_Employees (Total_Employees − Total_Attrition)
```

## Calculated Columns (created in Power Query)

| Column | Logic |
|---|---|
| Attrition Count | 1 if Attrition = "Yes", else 0 |
| Sorted Age | Numeric sort key for Age Group (has a known bug) |

## Design Notes

- Rates are computed as **measures**, not columns, so they re-aggregate correctly at any grouping (by role, department, etc.) instead of averaging averages.
- Measures reference other measures, so a change to `Total_Employees` flows through to the rate and active count.
