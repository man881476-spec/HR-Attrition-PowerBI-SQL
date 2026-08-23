# SQL Queries — HR Attrition Analysis

Database: SQLite (DB Browser for SQLite) · Table: `HR` (1,470 rows, IBM HR Analytics dataset)

## 1. Basic Aggregation

```sql
-- Overall attrition count (Yes vs No)
SELECT Attrition, count(*) as total
FROM HR
GROUP BY Attrition;

-- Total employee count
SELECT count(*) as total_employees
FROM HR;

-- Employees by department
SELECT department, count(*) as Employees
FROM HR
GROUP BY Department;

-- Employees by gender
SELECT Gender, count(*) as total_employees
FROM HR
GROUP BY Gender;

-- Average monthly income
SELECT round(avg(monthlyincome), 2)
FROM HR;

-- Highest salary
SELECT max(monthlyincome) as highest_salary
FROM HR;

-- Lowest salary
SELECT min(monthlyincome) as lowest_salary
FROM HR;

-- Average age
SELECT round(avg(age), 2) as average_age
FROM HR;

-- Employees by overtime status
SELECT overtime, count(overtime) as employees
FROM HR
GROUP BY overtime;

-- Employees by job role
SELECT JobRole, count(*) as Employees
FROM HR
GROUP BY JobRole;
```

## 2. Filtering (WHERE)

```sql
-- All female employees
SELECT *
FROM hr
WHERE Gender = 'Female';

-- All employees in Sales department
SELECT *
FROM hr
WHERE department = 'Sales';

-- All employees who left (attrition = Yes)
SELECT *
FROM hr
WHERE Attrition = 'Yes';

-- Female employees in Sales specifically
SELECT *
FROM hr
WHERE Department = 'Sales'
  AND Gender = 'Female';

-- Employees earning between 5,000 and 10,000
SELECT EmployeeNumber, MonthlyIncome
FROM hr
WHERE MonthlyIncome BETWEEN 5000 AND 10000;

-- Employees in Sales or Human Resources
SELECT *
FROM hr
WHERE Department IN ('Sales', 'Human Resources');
```

## 3. Sorting (ORDER BY / LIMIT)

```sql
-- All employees ranked by salary, highest first
SELECT EmployeeNumber, MonthlyIncome
FROM hr
ORDER BY MonthlyIncome DESC;

-- Top 10 highest-paid employees
SELECT EmployeeNumber, MonthlyIncome
FROM hr
ORDER BY MonthlyIncome DESC
LIMIT 10;
```

## 4. Grouping with HAVING

```sql
-- Departments with more than 100 employees
SELECT Department, count(EmployeeNumber)
FROM hr
GROUP BY department
HAVING count(EmployeeNumber) > 100;
```

## 5. Multi-Column Aggregation

```sql
-- Average salary by department
SELECT Department, ROUND(avg(Monthlyincome), 2)
FROM hr
GROUP BY department;

-- Average salary by job role
SELECT JobRole, ROUND(avg(Monthlyincome), 2) as AVG_Salary
FROM hr
GROUP BY JobRole;

-- Attrition breakdown by overtime status (both dimensions together)
SELECT OverTime, Attrition, count(*) as Employees
FROM hr
GROUP BY OverTime, Attrition;
```

## 6. Window Functions

```sql
-- Rank every employee by salary within their own department
-- (highest paid = rank 1, resets for each department)
SELECT EmployeeNumber, Department, MonthlyIncome,
       RANK() OVER (PARTITION BY Department ORDER BY MonthlyIncome DESC) as salary_rank
FROM hr;

-- Top 3 highest-paid employees in each department
SELECT *
FROM (
    SELECT EmployeeNumber, Department, JobRole, MonthlyIncome,
           ROW_NUMBER() OVER (PARTITION BY Department ORDER BY MonthlyIncome DESC) as rn
    FROM hr
)
WHERE rn <= 3;

-- Running (cumulative) count of employees ordered by salary,
-- lowest to highest — useful for building a manual salary distribution
SELECT EmployeeNumber, MonthlyIncome,
       COUNT(*) OVER (ORDER BY MonthlyIncome) as running_employee_count
FROM hr;
```

## 7. Subquery

```sql
-- Employees who earn more than their own department's average salary
SELECT EmployeeNumber, Department, MonthlyIncome
FROM hr h
WHERE MonthlyIncome > (
    SELECT AVG(MonthlyIncome)
    FROM hr
    WHERE Department = h.Department
);
```

## 8. CTE (Common Table Expression)

```sql
-- Attrition rate per department, then filter to departments
-- whose attrition rate is above the company-wide average
WITH dept_attrition AS (
    SELECT Department,
           COUNT(*) as total_employees,
           SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) as attrition_count,
           ROUND(100.0 * SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*), 2) as attrition_rate
    FROM hr
    GROUP BY Department
),
company_avg AS (
    SELECT ROUND(100.0 * SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*), 2) as avg_rate
    FROM hr
)
SELECT dept_attrition.*
FROM dept_attrition, company_avg
WHERE dept_attrition.attrition_rate > company_avg.avg_rate;
```
