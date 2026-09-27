# SQL Analytics Case Studies & Problem Sets

A structured collection of end-to-end SQL analysis tasks featuring real-world datasets, data cleaning, exploratory queries, business metrics, and comprehensive solution walk-throughs.

---

## 📌 Projects Overview

### 1. SQL Task 1: Student Scores & Academic Performance Analysis

* **Dataset:** `student-scores.csv`
* **Objective:** Perform academic data exploration, identify score distributions, evaluate student performance across subjects, and segment outcomes based on demographic factors.
* **Core Concepts Used:**
* Aggregation functions (`COUNT`, `AVG`, `MIN`, `MAX`)
* Data grouping and categorization (`GROUP BY`, `CASE WHEN`, `HAVING`)
* Ranking and filtering window functions
* Handling missing values and data validation

## 📊 Detailed SQL Solutions & Analytical Insights

---

### 📂 SQL Task 1: Student Scores & Academic Performance

#### 1. Average Subject Performance vs Career Aspirations

```sql
/* ==============================================================================
   SQL TASK 1: STUDENT SCORES & ACADEMIC ANALYSIS
   Dataset: student-scores.csv
   ============================================================================== */

-- 1. Display all student records
SELECT * 
FROM student_scores;

-- 2. Select distinct career aspirations
SELECT DISTINCT career_aspiration 
FROM student_scores;

-- 3. Filter students aspiring to become a 'Doctor'
SELECT * 
FROM student_scores 
WHERE career_aspiration = 'Doctor';

-- 4. Find students who scored more than 85 in Math
SELECT * 
FROM student_scores 
WHERE math_score > 85;

-- 5. Calculate average scores across all core subjects
SELECT 
    AVG(math_score) AS avg_math,
    AVG(history_score) AS avg_history,
    AVG(physics_score) AS avg_physics,
    AVG(chemistry_score) AS avg_chemistry,
    AVG(biology_score) AS avg_biology,
    AVG(english_score) AS avg_english,
    AVG(geography_score) AS avg_geography
FROM student_scores;

-- 6. Top 10 highest scorers in Math
SELECT id, first_name, last_name, math_score 
FROM student_scores 
ORDER BY math_score DESC 
LIMIT 10;

-- 7. Count number of students by gender
SELECT 
    gender, 
    COUNT(*) AS total_students 
FROM student_scores 
GROUP BY gender;

-- 8. Average total academic performance grouped by career aspiration
SELECT 
    career_aspiration,
    COUNT(*) AS total_students,
    ROUND(AVG(math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0, 2) AS overall_avg_score
FROM student_scores
GROUP BY career_aspiration
ORDER BY overall_avg_score DESC;

-- 9. Identify students with high study hours (>15 hrs/week) and their performance
SELECT 
    id, 
    first_name, 
    last_name, 
    weekly_self_study_hours, 
    math_score, 
    physics_score 
FROM student_scores 
WHERE weekly_self_study_hours > 15 
ORDER BY weekly_self_study_hours DESC;

-- 10. Grade classification based on average marks
SELECT 
    id,
    first_name,
    last_name,
    ROUND((math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0, 2) AS avg_score,
    CASE 
        WHEN (math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0 >= 90 THEN 'A+'
        WHEN (math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0 >= 80 THEN 'A'
        WHEN (math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0 >= 70 THEN 'B'
        WHEN (math_score + history_score + physics_score + chemistry_score + biology_score + english_score + geography_score) / 7.0 >= 60 THEN 'C'
        ELSE 'Fail / Needs Improvement'
    END AS academic_grade
FROM student_scores;
```

---

### 2. SQL Task 2: Coffee Shop Retail & Sales Analytics

* **Dataset:** `Coffee Shop Sales.csv`
* **Objective:** Analyze daily transaction trends, peak sales hours, store-level performance, top-performing product categories, and overall revenue drivers.
* **Core Concepts Used:**
* Date/Time transformations (extracting day, hour, month)
* Revenue and volume calculations (`SUM`, cumulative totals)
* Business KPIs: Average Order Value (AOV), footfall analysis, and store benchmarking
* Query optimization and CTEs (Common Table Expressions) for multi-step reporting
---

## 📊 Detailed SQL Solutions & Analytical Insights

---
### 📂 SQL Task 2: Coffee Shop Retail & Sales Analytics
## 1. Store Location Benchmarking & Average Order Value (AOV)

```sql
/* ==============================================================================
   SQL TASK 2: COFFEE SHOP RETAIL SALES ANALYSIS
   Dataset: Coffee Shop Sales.csv
   ============================================================================== */

-- 1. Preview transactional data
SELECT * 
FROM coffee_shop_sales 
LIMIT 100;

-- 2. Total revenue generated and total units sold
SELECT 
    ROUND(SUM(unit_price * transaction_qty), 2) AS total_revenue,
    SUM(transaction_qty) AS total_units_sold,
    COUNT(DISTINCT transaction_id) AS total_orders
FROM coffee_shop_sales;

-- 3. Monthly Sales Revenue Breakdown
SELECT 
    strftime('%Y-%m', transaction_date) AS sales_month,
    ROUND(SUM(unit_price * transaction_qty), 2) AS monthly_revenue,
    SUM(transaction_qty) AS units_sold
FROM coffee_shop_sales
GROUP BY sales_month
ORDER BY sales_month;

-- 4. Store Location Performance Comparison
SELECT 
    store_location,
    ROUND(SUM(unit_price * transaction_qty), 2) AS total_sales,
    COUNT(DISTINCT transaction_id) AS total_transactions,
    ROUND(SUM(unit_price * transaction_qty) / COUNT(DISTINCT transaction_id), 2) AS avg_order_value
FROM coffee_shop_sales
GROUP BY store_location
ORDER BY total_sales DESC;

-- 5. Top 5 Product Categories by Revenue
SELECT 
    product_category,
    ROUND(SUM(unit_price * transaction_qty), 2) AS revenue,
    SUM(transaction_qty) AS quantity_sold
FROM coffee_shop_sales
GROUP BY product_category
ORDER BY revenue DESC
LIMIT 5;

-- 6. Peak footfall and sales by Hour of the Day
SELECT 
    strftime('%H', transaction_time) AS order_hour,
    COUNT(DISTINCT transaction_id) AS transaction_count,
    ROUND(SUM(unit_price * transaction_qty), 2) AS total_revenue
FROM coffee_shop_sales
GROUP BY order_hour
ORDER BY transaction_count DESC;

-- 7. Day of Week sales trend (Peak business days)
SELECT 
    CASE strftime('%w', transaction_date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END AS day_of_week,
    ROUND(SUM(unit_price * transaction_qty), 2) AS total_sales,
    COUNT(DISTINCT transaction_id) AS order_volume
FROM coffee_shop_sales
GROUP BY strftime('%w', transaction_date)
ORDER BY total_sales DESC;

-- 8. Top 10 Best-Selling Individual Products
SELECT 
    product_detail,
    product_type,
    SUM(transaction_qty) AS total_qty_sold,
    ROUND(SUM(unit_price * transaction_qty), 2) AS total_revenue
FROM coffee_shop_sales
GROUP BY product_detail, product_type
ORDER BY total_revenue DESC
LIMIT 10;
```

## 🛠️ Tech Stack & Tools

* **Database Engine:** SQL / SQLite / PostgreSQL / MySQL
* **Tools:** GitHub, DB Browser for SQLite, DBeaver, or equivalent SQL workbench
* **File Formats:** CSV (Data Sources), PDF (Solutions & Explanations)
