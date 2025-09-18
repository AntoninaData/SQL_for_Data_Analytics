
## Problem LinkedIn 001 — Calculates the difference between the highest salaries
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
```

## Problem Microsoft 002 — Find current salary per employee
**Goal:** We have a table with employees and their salaries, however, some of the records are old and contain outdated salary information. Find the current salary of each employee assuming that salaries increase each year. Output their id, first name, last name, department ID, and current salary. Order your list by employee ID in ascending order.

```sql
-- Dialect: MySQL
SELECT 
    t.id,
    t.first_name,
    t.last_name,
    t.department_id,
    t.salary AS current_salary
FROM (
    SELECT 
        e.*,
        ROW_NUMBER() OVER (
            PARTITION BY e.id 
            ORDER BY e.salary DESC
        ) AS rn
    FROM ms_employee_salary e
) t
WHERE rn = 1
ORDER BY id;
```


## Problem Amazon 003 — Find job titles of the highest-paid employees
**Goal:** Find the job titles of the employees with the highest salary. If multiple employees have the same highest salary, include the job titles for all such employees.

```sql
-- Dialect: MySQL
SELECT b.worker_title AS best_paid_title
FROM worker a
JOIN title b ON a.worker_id = b.worker_ref_id
WHERE a.salary = (
    SELECT MAX(w.salary)
    FROM worker w
    JOIN title t ON w.worker_id = t.worker_ref_id
)
ORDER BY best_paid_title;
```


## Problem DoorDash 004 — Find the last time each bike was in use
**Goal:** Find the last time each bike was in use. Output both the bike number and the date-timestamp of the bike's last use (i.e., the date-time the bike was returned). Order the results by bikes that were most recently used.

```sql
-- Dialect: MySQL
SELECT t.bike_number,
       t.end_time AS most_recent_time
FROM (
    SELECT bike_number, 
           end_time, 
           ROW_NUMBER() OVER(
               PARTITION BY dc_bikeshare_q1_2012.bike_number
               ORDER BY end_time DESC
           ) AS rn
    FROM dc_bikeshare_q1_2012
) AS t
WHERE rn=1
ORDER BY t.end_time DESC;
```


## Problem Glassdoor 005 — Compare employee salary with department average
**Goal:** Compare each employee's salary with the average salary of the corresponding department. Output the department, first name, and salary of employees along with the average salary of that department.

```sql
-- Dialect: MySQL
SELECT e.first_name,
       e.department,
       e.salary,
       AVG(e.salary) OVER (PARTITION BY e.department) AS average_salary
FROM employee e
ORDER BY e.department;
```


## Problem Apple 006 — Find details of each customer with or without orders
**Goal:** Find the details of each customer regardless of whether the customer made an order. Output the customer's first name, last name, and the city along with the order details. Sort records based on the customer's first name and the order details in ascending order.

```sql
-- Dialect: MySQL
SELECT c.first_name,
       c.last_name,
       c.city,
       o.order_details
FROM customers c
LEFT JOIN orders o ON c.id=o.cust_id
ORDER BY c.first_name ASC, o.order_details ASC;
```


## Problem Amazon 007 — Find number of workers joined on or after April 1, 2014
**Goal:** Find the number of workers by department who joined on or after April 1, 2014. Output the department name along with the corresponding number of workers. Sort the results based on the number of workers in descending order.

```sql
-- Dialect: MySQL
SELECT department,
       COUNT(worker_id) AS Number_of_Workers
FROM worker
WHERE joining_date>='2014-04-01'
GROUP BY department
ORDER BY Number_of_Workers DESC;
```


## Problem Apple 008 — Number of unique users per client by month
**Goal:** Write a query that returns the number of unique users per client for each month. Assume all events occur within the same year, so only month needs to be in the output as a number from 1 to 12.

```sql
-- Dialect: MySQL
SELECT client_id,
       COUNT(DISTINCT(user_id)) AS Number_of_Customers,
       MONTH(time_id) AS Month_number
FROM fact_events
GROUP BY client_id, Month_number
ORDER BY client_id;
```


## Problem Airbnb 009 — Average number of bathrooms and bedrooms
**Goal:** Find the average number of bathrooms and bedrooms for each city’s property types. Output the result along with the city name and the property type.

```sql
-- Dialect: MySQL
SELECT a.city,
       a.property_type,
       AVG(a.bathrooms) AS bathrooms_average,
       AVG(a.bedrooms) AS bedrooms_average
FROM airbnb_search_details a
GROUP BY a.property_type, a.city;
```


## Problem Spotify 010 — Find songs ranked in the top position
**Goal:** Find songs that have ranked in the top position. Output the track name and the number of times it ranked at the top. Sort your records by the number of times the song was in the top position in descending order.

```sql
-- Dialect: MySQL
SELECT trackname,
       COUNT(trackname) AS Times_Top
FROM spotify_worldwide_daily_song_ranking
WHERE position=1
GROUP BY trackname
ORDER BY Times_Top DESC;
```


## Problem Spotify 011 — Find how many times each artist appeared
**Goal:** Find how many times each artist appeared on the Spotify ranking list. Output the artist name along with the corresponding number of occurrences. Order records by the number of occurrences in descending order.

```sql
-- Dialect: MySQL
SELECT artist,
       COUNT(id) AS Times_Appeared
FROM spotify_worldwide_daily_song_ranking
GROUP BY artist
ORDER BY Times_Appeared DESC;
```


## Problem Lyft 012 — Lyft drivers with extreme salaries
**Goal:** Find all Lyft drivers who earn either equal to or less than 30k USD or equal to or more than 70k USD. Output all details related to retrieved records.

```sql
-- Dialect: MySQL
SELECT *
FROM lyft_drivers
WHERE yearly_salary<=30000 OR yearly_salary>=70000;
```


## Problem Meta 013 — Average Hack popularity by location
**Goal:** Meta/Facebook has developed a new programing language called Hack. To measure the popularity of Hack they ran a survey with their employees. The survey included data on previous programing familiarity as well as the number of years of experience, age, gender and most importantly satisfaction with Hack. Due to an error location data was not collected, but your supervisor demands a report showing average popularity of Hack by office location. Luckily the user IDs of employees completing the surveys were stored. Based on the above, find the average popularity of the Hack per office location. Output the location along with the average popularity.

```sql
-- Dialect: MySQL
SELECT e.location,
       AVG(s.popularity) AS Popularyty_Location
FROM facebook_employees e
JOIN facebook_hack_survey s ON e.id=s.employee_id
GROUP BY e.location;
```

## Problem Shopify 014 — Orders made by Jill and Eva
**Goal:** Find order details made by Jill and Eva. Consider the Jill and Eva as first names of customers. Output the order date, details and cost along with the first name. Order records based on the customer id in ascending order.

```sql
-- Dialect: MySQL
SELECT o.order_date,
       o.order_details,
       o.total_order_cost,
       c.first_name
FROM customers c
JOIN orders o ON c.id=o.cust_id
WHERE c.first_name IN ('Jill','Eva')
ORDER BY c.id;
```

## Problem Amazon 015 — Number of shipments per month
**Goal:** Write a query that will calculate the number of shipments per month. The unique key for one shipment is a combination of `shipment_id` and `sub_id`. Output the `year_month` in format `YYYY-MM` and the number of shipments in that month.

```sql
-- Dialect: MySQL
SELECT CONCAT((shipment_date), '-', MONTH(shipment_date)) AS year_month
FROM amazon_shipment;
```


## Problem Apple 016 — Count the number of user events performed by MacBookPro users
**Goal:** Count the number of user events performed by MacBookPro users. Output the result along with the event name. Sort the result based on the event count in the descending order.

```sql
-- Dialect: MySQL
SELECT p.event_name,
       COUNT(p.user_id) AS event_count
FROM playbook_events p
WHERE p.device = 'macbook pro'
GROUP BY p.event_name
ORDER BY event_count DESC;
```


## Problem Forbes 017 — Find the most profitable company from the financial sector
**Goal:** Find the most profitable company from the financial sector. Output the result along with the continent.

```sql
-- Dialect: MySQL
SELECT 
    n.continent,
    n.company
FROM (
    SELECT * 
    FROM forbes_global_2010_2014 
    ORDER BY profits DESC
) n
LIMIT 1;
```


## Problem City of Los Angeles 018 — Inspection date and risk category for STREET CHURROS
**Goal:** Find the inspection date and risk category (`pe_description`) of facilities named 'STREET CHURROS' that received a score below 95.

```sql
-- Dialect: MySQL
SELECT activity_date,
       pe_description
FROM los_angeles_restaurant_health_inspections
WHERE facility_name = 'STREET CHURROS' 
  AND score < 95;
```


## Problem City of San Francisco 019 — Number of violations at Roxanne Cafe per year
**Goal:** Count the total number of violations that occurred at **'Roxanne Cafe'** for each year, based on the inspection date. Output the year and the corresponding number of violations in ascending order of the year.

```sql
-- Dialect: MySQL
SELECT YEAR(inspection_date) AS year,
       COUNT(violation_id) AS number_of_violations
FROM sf_restaurant_health_violations
WHERE violation_id IS NOT NULL 
  AND business_name = 'Roxanne Cafe'
GROUP BY YEAR(inspection_date)
ORDER BY year;
```


## Problem Microsoft 020 — Number of Admin employees who joined in April or later
**Goal:** Find the number of employees working in the Admin department that joined in April or later, in any year.

```sql
-- Dialect: MySQL
SELECT COUNT(worker_id) AS num_admin_employees
FROM worker
WHERE department = 'Admin' 
  AND MONTH(joining_date) >= 4;
```



