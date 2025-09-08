
## Problem 001 — Calculates the difference between the highest salaries
**Goal:** Calculates the difference between the highest salaries in the marketing and engineering departments. Output just the absolute difference in salaries.

```sql
-- Dialect: MySQL
SELECT 
    ABS(m.salary_marketing - e.salary_engineering) AS salary_difference
FROM 
    (
        SELECT MAX(emp.salary) AS salary_marketing
        FROM db_employee emp
        JOIN db_dept dep 
            ON emp.department_id = dep.id
        WHERE dep.department = 'Marketing'
    ) m,
    (
        SELECT MAX(emp.salary) AS salary_engineering
        FROM db_employee emp
        JOIN db_dept dep 
            ON emp.department_id = dep.id
        WHERE dep.department = 'Engineering'
    ) e;
