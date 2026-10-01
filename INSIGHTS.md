# Insights & Recommendations

Overall: **237 of 1,470 employees left, an attrition rate of 16.1%.**

> All rates below are calculated from the data in the model (`n` = group size). They show **association, not causation**, and some groups are small. Treat n < 100 with caution.

## 1. Workload and travel

| Segment | n | Attrition rate |
|---|---|---|
| Works overtime | 416 | **30.5%** |
| No overtime | 1,054 | 10.4% |
| Travels frequently | 277 | **24.9%** |
| Travels rarely | 1,043 | 15.0% |
| Non-travel | 150 | 8.0% |

Overtime employees leave at nearly **3x** the rate of others, the biggest single gap in the dataset.

## 2. Age and life stage

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

## 3. Role and department

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

## 4. Pay, level and satisfaction

| Metric | Left | Stayed |
|---|---|---|
| Avg monthly income | 4,787 | 6,833 |
| Avg age | 33.6 | 37.6 |
| Avg years at company | 5.1 | 7.4 |
| Avg distance from home | 10.6 | 8.9 |

- **Job Level 1** attrition is 26.3%, versus 4.7%–14.7% at higher levels.
- **Job Satisfaction 1** (lowest) has 22.8% attrition vs 11.3% at level 4.
- **Work Life Balance 1** has 31.2% attrition, but n = 80, so interpret cautiously.

## 5. Education

Education level shows only a weak pattern (High School 18.2% down to Doctoral 10.4%, n = 48). By field, Human Resources (25.9%, n = 27), Technical Degree (24.2%) and Marketing (22.0%) are highest. Life Sciences has the most leavers in absolute count (89) but a mid-range rate (14.7%).

## Recommendations

1. **Review overtime and workload**, particularly for Sales and Lab roles, where retention risk is concentrated.
2. **Target early-career retention** (under 25 and Job Level 1) with onboarding, mentoring and clear progression paths.
3. **Investigate Sales Representative turnover**: check pay, targets and management. At 39.8% it is a prime candidate for an exit-interview programme.
4. **Look at travel policies** for frequent travellers: caps, rotation, or compensation.
5. **Benchmark pay for junior roles**, since leavers earn about 30% less on average (4,787 vs 6,833).
6. **Track satisfaction and work-life balance** as early-warning indicators.

## Suggested Next Steps

- Add statistical testing or a simple logistic regression to see which factors hold up after controlling for each other (overtime, level and income are likely correlated).
- Add trend data (attrition by period) if dates become available.
