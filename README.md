# SQL_RETAIL_SALES_DATA_ANALYST_PROJECT

# Retail Sales Analysis 🛒

A beginner-level SQL project to explore, clean, and analyze retail sales data using PostgreSQL.

---

## 📋 Project Overview

| Detail | Info |
|--------|------|
| **Title** | Retail Sales Analysis |
| **Level** | Beginner |
| **Database** | `p1_retail_db` |
| **Tool** | PostgreSQL |

---

## 🎯 Objectives

- Set up a retail sales database and populate it with sales data
- Clean data by identifying and removing null/missing records
- Perform exploratory data analysis (EDA)
- Answer business questions using SQL queries

---

## 🗂️ Project Structure

### 1. Database Setup

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales
(
    transactions_id  INT PRIMARY KEY,
    sale_date        DATE,
    sale_time        TIME,
    customer_id      INT,
    gender           VARCHAR(10),
    age              INT,
    category         VARCHAR(35),
    quantity         INT,
    price_per_unit   FLOAT,
    cogs             FLOAT,
    total_sale       FLOAT
);
```

---

### 2. Data Exploration & Cleaning

```sql
-- Total records
SELECT COUNT(*) FROM retail_sales;

-- Unique customers
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

-- Unique categories
SELECT DISTINCT category FROM retail_sales;

-- Check for nulls
SELECT * FROM retail_sales
WHERE
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR
    gender IS NULL OR age IS NULL OR category IS NULL OR
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

-- Remove null records
DELETE FROM retail_sales
WHERE
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR
    gender IS NULL OR age IS NULL OR category IS NULL OR
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

---

### 3. Business Analysis Queries

**Q1. Sales on a specific date**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Q2. Clothing sales with quantity ≥ 4 in Nov-2022**
```sql
SELECT *
FROM retail_sales
WHERE
    category = 'Clothing'
    AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND quantity >= 4;
```

**Q3. Total sales per category**
```sql
SELECT
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*)        AS total_orders
FROM retail_sales
GROUP BY category;
```

**Q4. Average age of Beauty category customers**
```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Q5. Transactions with total sale > 1000**
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Q6. Transaction count by gender and category**
```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Q7. Best selling month per year**
```sql
SELECT year, month, avg_sale
FROM (
    SELECT
        EXTRACT(YEAR FROM sale_date)  AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale)               AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

**Q8. Top 5 customers by total sales**
```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Q9. Unique customers per category**
```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

**Q10. Orders by shift (Morning / Afternoon / Evening)**
```sql
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12              THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

## 📊 Findings

- **Customer Demographics** — Customers span various age groups across categories like Clothing and Beauty
- **High-Value Transactions** — Several transactions exceed $1,000, indicating premium purchases
- **Sales Trends** — Monthly analysis reveals peak seasons and best-performing months per year
- **Customer Insights** — Top-spending customers and most popular categories identified

---

## 📁 Reports

| Report | Description |
|--------|-------------|
| Sales Summary | Total sales, customer demographics, category performance |
| Trend Analysis | Sales trends across months and time shifts |
| Customer Insights | Top customers and unique customer counts per category |

---

## 🚀 How to Use

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   ```

2. **Set up the database** — Run `database_setup.sql` to create and populate the database

3. **Run the queries** — Use `analysis_queries.sql` to perform the analysis

4. **Explore** — Modify queries to answer additional business questions

---

## 🛠️ Tech Stack

- **Database:** PostgreSQL
- **Language:** SQL
- **Concepts:** DDL, DML, Aggregations, Window Functions, CTEs, Date Functions

---

## 📌 Conclusion

This project provides a hands-on introduction to SQL for data analysts — covering database setup, data cleaning, EDA, and business-driven queries. The insights derived can help drive decisions around sales patterns, customer behavior, and product performance.
