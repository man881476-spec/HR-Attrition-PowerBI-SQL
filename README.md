# HR Employee Attrition Analysis

Interactive Power BI dashboard analyzing employee attrition for a 1,470-person organization, built on the IBM HR Analytics dataset. Goal was to figure out where the company is actually losing people and why, instead of just reporting the overall attrition number.

## Why this project

A 16% attrition rate on its own doesn't tell HR anything actionable. Anyone can see "16% of people left." The real question is which departments, roles, and pay bands are driving that number, and whether the drivers are things a company can actually fix (workload, pay) or things it can't (personal circumstances). I built this to practice going from raw HR data to conclusions someone could act on.

## What's in the dashboard

Five pages, all connected through the same filter set (Department, Job Role, Education Field, Gender):

- **Overview** - company-wide numbers: headcount, attrition rate, department split, overtime split
- **Employees Attrition Analysis** - breaks attrition down by job role, department, overtime, and travel frequency
- **Employees Risk Analysis** - attrition by age group, income band, satisfaction score, and tenure, plus a Key Influencers visual that ranks which factors actually move the needle
- **Salary and Performance Analysis** - salary by department/role next to performance ratings, work-life balance, and training data, to check whether pay or performance explains attrition
- **Executive Insight** - attrition by marital status, education, and commute distance, plus a summary of root causes and what I'd recommend HR do about them

## What the data actually shows

Overtime turned out to be the biggest signal by far. People working overtime are only 28% of the workforce but make up 54% of everyone who left - Power BI's Key Influencers feature shows a 2.93x higher likelihood of attrition. That was bigger than I expected going in; I assumed pay would dominate.

Pay still matters, just not evenly. Nearly half the attrition comes from people earning under $3K a month, and low pay keeps showing up as a factor even after accounting for performance - high performers on low pay still leave at high rates, which suggests the fix is compensation, not more "engagement" initiatives.

Tenure is the other clear pattern: 43% of everyone who left had been there 2 years or less. That's a very front-loaded curve, and it usually points to either a hiring/expectations mismatch or weak onboarding rather than a company-wide culture problem.

By department, R&D and Sales account for the large majority of departures (133 and 92 out of 237), which - combined with average salaries in those groups - represents roughly $13.6M in annual salary exposure associated with employees who left.

A few things did NOT hold up the way I expected: job satisfaction scores don't cleanly predict who leaves (the "somewhat satisfied" group had the highest attrition count, not the lowest), and Sales has the highest average department salary but is still one of the top two sources of attrition - so whatever's driving it there isn't pay.

## If I were advising HR on this

1. Look at overtime policy first - it's the strongest single lever in the data. Either the workload needs redistributing or the overtime needs to be worth it.
2. Revisit the compensation floor for the sub-$3K band specifically, rather than an across-the-board raise.
3. Fix onboarding/first-two-years support - that's where almost half the losses happen.
4. Dig into Sales separately from a pay lens - exit interviews, workload, quota pressure - since pay isn't the obvious explanation there.

## Tools

Power BI Desktop for the dashboard, DAX for the custom measures (attrition rate, active headcount, salary-at-risk), and SQLite (DB Browser) for a separate set of SQL queries against the same dataset - basic aggregations plus window functions, a correlated subquery, and a CTE. Those are in `SQL_Queries.md` with screenshots showing them run against the actual data.

## Files here
├── README.md
├── HR_Attrition_Dashboard.pbix
├── HR-Employee-Attrition.csv
├── HR-Employee-Attrition.xlsx
├── HR-Employee-Attrition.pdf
├── SQL_Queries.md
├── SQL_Queries_Proof.pdf
│
└── screenshots/
    ├── 01_Overview.png
    ├── 02_Attrition_Analysis.png
    ├── 03_Risk_Analysis.png
    ├── 04_Salary_Performance.png
    └── 05_Executive_Insight.png
