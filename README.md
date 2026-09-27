# 📊 SQL Analytics Case Studies & Problem Sets

A structured collection of end-to-end SQL analysis tasks featuring real-world datasets, exploratory data analysis (EDA), business intelligence metrics, and comprehensive query walkthroughs.

---

## 📑 Table of Contents

1. [📌 Projects Overview](#-projects-overview)
2. [🛠️ Tech Stack & Tools](#️-tech-stack--tools)
3. [📂 Task 1: Student Scores & Academic Performance Analysis](#-task-1-student-scores--academic-performance-analysis)
  - [Problem Statements](#problem-statements-task-1)
  - [SQL Solutions & Insights](#sql-solutions-task-1)
4. [📂 Task 2: Coffee Shop Retail & Sales Analytics](#-task-2-coffee-shop-retail--sales-analytics)
  - [Problem Statements](#problem-statements-task-2)
  - [SQL Solutions & Insights](#sql-solutions-task-2)
5. [👤 Author](#-author)

---

## 📌 Projects Overview

| Project | Dataset | Key Techniques | Primary Objective |
| :--- | :--- | :--- | :--- |
| **Task 1: Student Scores** | `student-scores.csv` | Aggregations, Window Functions (`RANK`), Subqueries, `CASE WHEN` | Academic benchmarking, demographic comparison, and multi-subject evaluation. |
| **Task 2: Coffee Shop Sales** | `Coffee Shop Sales.csv` | Time-Series analysis, CTEs, Window Functions, KPI Benchmarking | Revenue drivers, peak hour traffic, inventory demand, and store efficiency. |

---

## 🛠️ Tech Stack & Tools

- **Database Engines:** PostgreSQL
- **Tools:** pgAdmin
- **Key Concepts:** Window Functions, Multi-Level Subqueries, CTEs, Date/Time Parsing, String Operations, Conditional Aggregation

---

## 📂 Task 1: Student Scores & Academic Performance Analysis

### Problem Statements (Task 1)

1. **Math Performance by Career:** Calculate the average `math_score` for each `career_aspiration`, ordered in descending order.
2. **High English Proficiency:** Find career aspirations with an average `english_score` greater than 75.
3. **Above School Average (Math):** Identify students scoring higher than the school-wide average `math_score`.
4. **Physics Ranking within Careers:** Rank students within each `career_aspiration` by their `physics_score` in descending order.
5. **String Filtering:** Create a column `full_name` (`first_name` + `last_name`) and filter records where `email` contains `'academy'`.
6. **Chemistry Statistics:** Calculate the lowest (`FLOOR`), highest (`CEIL`), and average (`ROUND` to 2 decimals) `chemistry_score` for each `career_aspiration`.
7. **Targeted Career Interest:** Find career paths where average `history_score` exceeds 85 with at least 5 aspiring students.
8. **Dual-Subject Outperformers:** Identify students scoring above average in **both** biology and chemistry.
9. **Absence Proportion:** Calculate each student's absence percentage relative to the overall absence pool.
10. **High Achiever Threshold:** Identify students scoring above 80 in at least 3 out of 6 core subjects.

### SQL Solutions (Task 1)

```sql
/* ==============================================================================
   SQL TASK 1: STUDENT SCORES & ACADEMIC ANALYSIS
   Dataset: student-scores.csv
   ============================================================================== */

--1)Calculate the average math_score for each career_aspiration. Order the results by the
average score in descending order.
SELECT career_aspiration, AVG(math_score) AS avg_math_score
FROMstudent_scores
GROUPBYcareer_aspiration
ORDERBYavg_math_score DESC;

--2)Find the career_aspirations that have an average english_score greater than 75. Display the
career aspiration and the average score.
SELECT career_aspiration, AVG(english_score) AS avg_english_score
FROMstudent_scores
GROUPBYcareer_aspiration
HAVING AVG(english_score) > 75
ORDERBYavg_english_score DESC;

--3)Identify students who have a math_score higher than the school's average math score. List
their first_name, last_name, and math_score.
SELECT first_name, last_name, math_score
FROMstudent_scores
WHEREmath_score > (SELECT AVG(math_score) FROM student_scores);

--4)Rank students within each career_aspiration category by their physics_score in descending
order. Display the first_name, last_name, career_aspiration, physics_score, and the rank.
SELECT first_name, last_name, career_aspiration, physics_score,
RANK() OVER (PARTITION BY career_aspiration ORDER BY physics_score DESC) AS
rank_in_career
FROMstudent_scores
ORDERBYcareer_aspiration, rank_in_career;

--5) For each student, create a new column full_name by concatenating first_name and
last_name with a space in between. Show the full_name and email columns where the email
contains the string "academy".
SELECT CONCAT(first_name, ' ', last_name) AS full_name, email
FROMstudent_scores
WHEREemail LIKE '%academy%';

--6)Calculate the lowest (FLOOR), highest (CEIL), and average (ROUND to two decimal places)
chemistry_score for each carreraspirats. Display the carrer aspiratns
, lowest score, highest score, and average score.
SELECT gender,
FLOOR(MIN(chemistry_score)) AS lowest_score,
CEIL(MAX(chemistry_score)) AS highest_score,
ROUND(AVG(chemistry_score), 2) AS average_score
FROMstudent_scores
GROUPBYgender;

--7)Find career aspirations where the average history_score is above 85 and at least 5 students
aspire to that career. List the career_aspiration and the average score.
SELECT career_aspiration, AVG(history_score) AS average_history_score
FROMstudent_scores
GROUPBYcareer_aspiration
HAVING AVG(history_score) > 85 AND COUNT(id) >= 5;

--8)Identify students who score above average in both biology and chemistry, compared to the
school's average for those subjects. Display their id, first_name, last_name, biology_score, and
chemistry_score.
SELECT id, first_name, last_name, biology_score, chemistry_score
FROMstudent_scores
WHEREbiology_score > (SELECT AVG(biology_score) FROM student_scores)
ANDchemistry_score > (SELECT AVG(chemistry_score) FROM student_scores);

--9)Calculate the percentage of absence days for each student relative to the total absence
days recorded for all students. Display the id, first_name, last_name, and the calculated
percentage, rounded to two decimal places. Order the results by the percentage in descending
ord
SELECT id, first_name, last_name,
ROUND((absence_days::decimal / (SELECT SUM(absence_days) FROM student_scores)
* 100), 2) AS absence_percentage
FROMstudent_scores
ORDERBYabsence_percentage DESC;

--10)Identify students who have scores above 80 in at least three out of the six subjects: math,
history, physics, chemistry, biology, and English. Display their id, first_name, last_name, and the
count of subjects where they scored above 80.
SELECT id, first_name, last_name,
(CASE WHENmath_score > 80 THEN 1 ELSE 0 END +
CASE WHENhistory_score > 80 THEN 1 ELSE 0 END +
CASE WHENphysics_score > 80 THEN 1 ELSE 0 END +
CASE WHENchemistry_score > 80 THEN 1 ELSE 0 END +
CASE WHENbiology_score > 80 THEN 1 ELSE 0 END +
CASE WHENenglish_score > 80 THEN 1 ELSE 0 END) AS subjects_above_80
FROMstudent_scores
WHERE(CASEWHENmath_score > 80 THEN 1 ELSE 0 END+
CASE WHENhistory_score > 80 THEN 1 ELSE 0 END +
CASE WHENphysics_score > 80 THEN 1 ELSE 0 END +
CASE WHENchemistry_score > 80 THEN 1 ELSE 0 END +
CASE WHENbiology_score > 80 THEN 1 ELSE 0 END +
CASE WHENenglish_score > 80 THEN 1 ELSE 0 END) >= 3;

```

---

## 📂 Task 2: Coffee Shop Retail & Sales Analytics

### Problem Statements (Task 2)

1) **Product Sales Analysis:**
- **Top 5 Most Frequently Sold Products by Product Category:**
   - Retrieve the top 5 products with the highest sales frequency within each product category.
   - **Purpose:** Identify the most popular products to inform inventory management and marketing strategies.


2) **Monthly Revenue Calculation:**
- **Total Revenue Generated by Each Store in January 2023:**
  - Calculate the total revenue for each store during January 2023.
  - **Purpose:** Assess store performance and compare revenue across different locations for that month.


3) **Product Variety in Specific Location:**
- **Unique Product Types Sold in 'Lower Manhattan' Store:**
  - List all unique product types sold in the store located in Lower Manhattan.
  - **Purpose:** Understand the product range available at this location to tailor offerings or promotions.


4) **Transaction Timing Analysis:**
- **Total Number of Transactions Before 12:00 PM:**
  - Calculate the total number of transactions that occurred before 12:00 PM on any given day.
  - **Purpose:** Optimize staffing and resource allocation during morning hours.

5) **Revenue Per Transaction Analysis:**
- **Average Revenue per Transaction for Each Product Category During Peak and Non-Peak Hours:**
   - Find the average revenue per transaction for each product category during peak hours (7 AM - 9 AM) and non-peak hours for each store.
   - **Purpose:** Understand customer spending patterns to adjust pricing or promotions accordingly.

6) **Price Variability Analysis:**
- **Product with the Most Price Fluctuations:**
   - Retrieve the product that has the largest difference between its highest and lowest price across all transactions.
   - **Purpose:** Identify pricing inconsistencies or opportunities for price stabilization.

7) **Product Availability Across All Stores:**
- **Products Sold in Every Store at Least Once:**
   - List all products that have been sold in every store at least once.
   - **Purpose:** Recognize core products that are essential across all locations.

8) **Transaction Quantity Deviations:**
- **Top 5 Days with the Highest Deviation from Average Daily Transaction Quantity:**
    - Identify the top 5 days where the total transaction quantity deviated the most from the average daily transaction quantity.
    - Purpose: Detect anomalies or events impacting sales volume for further investigation.

9) **High-Value Store Analysis:**
- **Store Location and Total Revenue Where Average Unit Price > $2.50:**
    - Retrieve store locations and total revenue for stores where the average unit price is greater than $2.50.
    - **Purpose:** Focus on stores with higher-priced sales for targeted strategies.

10) **Product Sales Efficiency:**
- **Product with the Highest Average Sales Quantity per Transaction in Each Store:**
     - Identify the product that has the highest average quantity sold per transaction in each store.
     - **Purpose:** Determine products that drive bulk purchases to enhance marketing efforts.


### SQL Solutions (Task 2)


```sql
/* ==============================================================================
   SQL TASK 2: COFFEE SHOP RETAIL SALES ANALYSIS
   Dataset: Coffee Shop Sales.csv
   ============================================================================== */

-- 1)Retrieve the top 5 most frequently sold products by product category.
SELECT product_category, product_id, SUM(transaction_qty) AS total_quantity_sold
FROMTransactions
GROUPBYproduct_category, product_id
ORDERBYtotal_quantity_sold DESC
LIMIT 5;

-- 2)Calculate the total revenue generated by each store in January 2023
SELECT store_id, store_location, SUM(transaction_qty * unit_price) AS total_revenue
FROMTransactions
WHEREtransaction_date BETWEEN '2023-01-01' AND '2023-01-31'
GROUPBYstore_id, store_location;

-- 3)List the unique product types sold in the store located in 'Lower Manhattan
SELECT DISTINCT product_type
FROMTransactions
WHEREstore_location = 'Lower Manhattan';

-- 4)Calculate the total number of transactions that occurred before 12:00 PM on any given day,
SELECT COUNT(transaction_id) AS total_transactions
FROMTransactions
WHEREtransaction_time < '12:00:00';

-- 5) Find the average revenue per transaction for each product category during peak hours (7 AM- 9 AM) and non-peak hours for each store.
SELECT
store_id,
store_location,
product_category,
AVG(CASE WHENtransaction_time BETWEEN '07:00:00' AND '09:00:00'
THEN transaction_qty * unit_price
END) AS avg_revenue_peak_hours,
AVG(CASE WHENtransaction_time NOT BETWEEN '07:00:00' AND '09:00:00'
THEN transaction_qty * unit_price
END) AS avg_revenue_non_peak_hours
FROMTransactions
GROUPBYstore_id, store_location, product_category;

-- 6)Retrieve the product with the most price fluctuations (i.e., the largest difference between the
highest and lowest price) across all transactions.
SELECT product_id, product_detail,
MAX(unit_price)- MIN(unit_price) AS price_fluctuation
FROMTransactions
GROUPBYproduct_id ,product_detail
ORDERBYprice_fluctuation DESC
LIMIT 1;

-- 7)List all the products that were sold in every store at least once.
SELECT product_detail
FROMTransactions
GROUPBYproduct_detail
HAVING COUNT(DISTINCT store_id) = (SELECT COUNT(DISTINCT store_id) FROM
Transactions);

--8) Identify the top 5 days where the total transaction quantity deviated the most from the
average daily transaction quantity. in postgresql
SELECT transaction_date,
total_qty,
deviation
FROM(
SELECT transaction_date,
SUM(transaction_qty) AS total_qty,
AVG(SUM(transaction_qty)) OVER () AS avg_qty,
ABS(SUM(transaction_qty)- AVG(SUM(transaction_qty)) OVER ()) AS deviation
FROMTransactions
GROUPBYtransaction_date
) AS Daily_Transactions
ORDERBYdeviation DESC
LIMIT 5;

-- 9)Retrieve the store location and total revenue for each store where the average unit price is
greater than $2.50.
SELECT
store_location,
SUM(transaction_qty * unit_price) AS total_revenue
FROM
Transactions
GROUPBY
store_id, store_location
HAVING
AVG(unit_price) > 2.50
ORDERBY
total_revenue DESC;

-- 10)Identify the product that has the highest average sales quantity per transaction in each store.
SELECT
t1.store_id,
t1.store_location,
t1.product_id,
t1.product_detail,
t1.avg_quantity
FROM(
SELECT
store_id,
store_location,
product_id,
product_detail,
AVG(transaction_qty) AS avg_quantity
FROM
Transactions
GROUPBY
store_id, store_location, product_id, product_detail
) t1
JOIN (
SELECT
store_id,
MAX(avg_quantity) AS max_avg_quantity
FROM(
SELECT
store_id,
product_id,
AVG(transaction_qty) AS avg_quantity
FROM
Transactions
GROUPBY
store_id, product_id
) sub
GROUPBY
store_id
) t2 ON t1.store_id = t2.store_id AND t1.avg_quantity = t2.max_avg_quantity
ORDERBY
t1.store_id;
```

---

## 👤 Author
- **LinkedIn:** [Your LinkedIn Profile URL](https://www.linkedin.com/in/ritik-panwar-01a67a24b/?lipi=urn%3Ali%3Apage%3Ad_flagship3_feed%3BzPr0zrYGSguyR5pNTkBafQ%3D%3D)
- **GitHub:** [Your GitHub Profile URL](https://github.com/ritikpanwar10/)
- **Portfolio:** [Your Portfolio / Project Link](https://ritikpanwar10.github.io/ritikpanwar.github.io/)
