# HR Analytics Dashboard — Employee Attrition (Power BI)

An interactive, single-page Power BI dashboard that analyses **employee attrition** across 1,470 employees. It answers a core HR question: **who is leaving, and what do they have in common?**

![Dashboard preview](docs/images/dashboard.png)
<!-- Add a screenshot: Power BI Desktop > File > Export > or Win+Shift+S, save as docs/images/dashboard.png -->

---

## Key Results

| KPI | Value |
|---|---|
| Total employees | **1,470** |
| Employees who left (attrition) | **237** |
| Active employees | **1,233** |
| **Attrition rate** | **16.1%** |
| Average age | 36.9 years |
| Average monthly income | 6,503 |

### Headline findings

1. **Overtime is the strongest signal.** 30.5% of employees working overtime left, versus 10.4% of those who do not (~2.9x).
2. **Young employees leave most.** Under-25s have a 39.2% attrition rate; ages 35–54 sit near 10%.
3. **Sales Representatives are the highest-risk role** (39.8%, 33 of 83), while Research Directors are the lowest (2.5%).
4. **Pay and seniority matter.** Leavers averaged 4,787 monthly income vs 6,833 for stayers; Job Level 1 has a 26.3% attrition rate.
5. **Frequent travellers and single employees** leave at roughly double the rate of their counterparts (24.9% vs 8.0% non-travel; 25.5% single vs 12.5% married).

Full breakdown with sample sizes and caveats: [docs/INSIGHTS.md](docs/INSIGHTS.md).

---

## Dashboard Features

- **6 KPI cards** — Total Employees, Total Attrition, Attrition Rate, Active Employees, Avg Age, Avg Monthly Income
- **6 analytical visuals** — headcount by age group and gender, attrition across age, department split, attrition rate by job role, job role × job satisfaction matrix, attrition by education field
- **4 slicers** — Education, Gender, Marital Status, Business Travel, plus a **Clear all slicers** button
- Cross-filtering between every visual

See [docs/DASHBOARD_GUIDE.md](docs/DASHBOARD_GUIDE.md) for the visual-by-visual breakdown.

---

## Tech Stack

| Layer | Tool |
|---|---|
| Source data | Excel workbook (`Company Data.xlsx`, sheet `HR data`) |
| ETL | Power Query (M) |
| Modelling & measures | Power BI data model, DAX |
| Visualisation | Power BI Desktop |

## Repository Structure

```
HR-Analytics-Dashboard/
├── README.md
├── powerbi/
│   └── HR_Dashboard.pbix          # The dashboard (open in Power BI Desktop)
├── data/
│   └── hr_data_export.csv         # Cleaned table as loaded into the model (1,470 rows x 26 cols)
└── docs/
    ├── DATA_DICTIONARY.md         # Every column: type, range, meaning
    ├── DATA_PREPARATION.md        # Power Query steps + data quality checks
    ├── DATA_MODEL_AND_DAX.md      # Model design and all DAX measures
    ├── DASHBOARD_GUIDE.md         # Visual-by-visual guide and how to use it
    ├── INSIGHTS.md                # Findings and recommendations
    ├── KNOWN_ISSUES_AND_ROADMAP.md
    └── images/                    # Screenshots
```

## Getting Started

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows).
2. Open `powerbi/HR_Dashboard.pbix`.
3. If you see a data source error, go to **Home > Transform data > Data source settings** and repoint the Excel source to your local copy of the data (the original path was a local Downloads folder). Alternatively, replace the source with `data/hr_data_export.csv` — see [DATA_PREPARATION.md](docs/DATA_PREPARATION.md#re-pointing-the-data-source).
4. Use the slicers on the left and top to filter; click any chart element to cross-filter.

## Skills Demonstrated

Data cleaning and transformation (Power Query) · Calculated columns and DAX measures · Dashboard layout and storytelling · KPI design · Attrition / HR analytics · Documentation

## Known Limitations

This is a single-table model and attrition findings are **correlations, not proof of cause**. A few visuals and a sort helper column have known issues, listed with fixes in [docs/KNOWN_ISSUES_AND_ROADMAP.md](docs/KNOWN_ISSUES_AND_ROADMAP.md).

## Author

**<Your Name>** — Data Analyst (transitioning from Software Test Engineering)
[LinkedIn](https://linkedin.com/in/your-profile) · [GitHub](https://github.com/your-username)
