# Walmart Sales Analysis SQL Project

## Project Overview

**Project Title**: Walmart Sales Analysis 
**Database**: `walmartsales`

This project uses SQL to explore and analyze Walmart sales data. It involves creating a database, setting up the `sales` table, cleaning the dataset, deriving new columns, and answering business questions related to revenue, ratings, customers, and product performance.

## Objectives

1. **Database Setup**: Create and populate the Walmart sales database.
2. **Data Cleaning & Transformation**: Add computed columns like time of day, day name, and month name.
3. **Exploratory Data Analysis (EDA)**: Understand customer demographics, sales patterns, and revenue streams.
4. **Business Insights**: Use SQL queries to answer key business questions.

## Project Structure

### 1. Database Setup

```sql
CREATE DATABASE IF NOT EXISTS walmartSales;

CREATE TABLE IF NOT EXISTS sales(
    invoice_id VARCHAR(30) NOT NULL PRIMARY KEY,
    branch VARCHAR(5) NOT NULL,
    city VARCHAR(30) NOT NULL,
    customer_type VARCHAR(30) NOT NULL,
    gender VARCHAR(10) NOT NULL,
    product_line VARCHAR(100) NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    quantity INT NOT NULL,
    tax_pct FLOAT(6,4) NOT NULL,
    total DECIMAL(12,4) NOT NULL,
    date DATETIME NOT NULL,
    time TIME NOT NULL,
    payment VARCHAR(15) NOT NULL,
    cogs DECIMAL(10,2) NOT NULL,
    gross_margin_pct FLOAT(11,9),
    gross_income DECIMAL(12,4) NOT NULL,
    rating FLOAT(2,1)
);
```

### 2. Data Transformation

- **Add time of day column**
```sql
ALTER TABLE sales ADD COLUMN time_of_day VARCHAR(20);
UPDATE sales
SET time_of_day = (
    CASE
        WHEN `time` BETWEEN '00:00:00' AND '12:00:00' THEN 'Morning'
        WHEN `time` BETWEEN '12:01:00' AND '16:00:00' THEN 'Afternoon'
        ELSE 'Evening'
    END
);
```

- **Add day name column**
```sql
ALTER TABLE sales ADD COLUMN day_name VARCHAR(10);
UPDATE sales
SET day_name = DAYNAME(date);
```

- **Add month name column**
```sql
ALTER TABLE sales ADD COLUMN month_name VARCHAR(10);
UPDATE sales
SET month_name = MONTHNAME(date);
```

### 3. Data Exploration

The following SQL queries were developed to answer specific business questions:

- **Unique Cities**
```sql
SELECT DISTINCT city FROM sales;
```

- **Branch-to-City Mapping**
```sql
SELECT DISTINCT city, branch FROM sales;
```

- **Unique Product Lines**
```sql
SELECT DISTINCT product_line FROM sales;
```

- **Unique Customer Types & Payment Methods**
```sql
SELECT DISTINCT customer_type FROM sales;
SELECT DISTINCT payment FROM sales;
```

### 3. Business Queries

The following SQL queries were developed to answer specific business questions:

1. **Most Selling Product Line**
```sql
SELECT
    product_line,
    SUM(quantity) AS qty
FROM sales
GROUP BY product_line
ORDER BY qty DESC;
```

2. **Total Revenue by Month**
```sql
SELECT
    month_name AS month,
    SUM(total) AS total_revenue
FROM sales
GROUP BY month_name
ORDER BY total_revenue DESC;
```

3. **Product Line with Highest VAT**
```sql
SELECT
    product_line,
    AVG(tax_pct) AS avg_tax
FROM sales
GROUP BY product_line
ORDER BY avg_tax DESC;
```

4. **Best Performing Branches**
```sql
SELECT
    branch,
    SUM(quantity) AS qnty
FROM sales
GROUP BY branch
HAVING SUM(quantity) > (SELECT AVG(quantity) FROM sales);
```

5. **Average Product Rating per Product Line**
```sql
SELECT
    product_line,
    ROUND(AVG(rating), 2) AS avg_rating
FROM sales
GROUP BY product_line
ORDER BY avg_rating DESC;
```

6. **Gender Distribution per Branch**
```sql
SELECT
    branch,
    gender,
    COUNT(*) AS gender_cnt
FROM sales
GROUP BY branch, gender
ORDER BY branch, gender_cnt DESC;
```

7. **Time of Day Ratings**
```sql
SELECT
    time_of_day,
    AVG(rating) AS avg_rating
FROM sales
GROUP BY time_of_day
ORDER BY avg_rating DESC;
```

8. **Best Day for Ratings**
```sql
SELECT
    day_name,
    AVG(rating) AS avg_rating
FROM sales
GROUP BY day_name
ORDER BY avg_rating DESC;
```

9. **City with Largest Revenue**
```sql
SELECT
    city,
    branch,
    SUM(total) AS total_revenue
FROM sales
GROUP BY city, branch
ORDER BY total_revenue DESC;
```

## Findings

- **Top Selling Products**: Certain product lines dominate in quantity sold.
- **Peak Sales Months**: Some months contribute significantly more revenue.
- **Branch Insights**: Branches A and C perform better on customer ratings than B.
- **Customer Behavior**: Mornings and evenings drive higher volumes, but ratings stay consistent.
- **Gender & Demographics**: Gender distribution is balanced across branches.

## Reports

- **Revenue Report**: Month-wise, branch-wise, and product-wise revenue insights.
- **Performance Analysis**: Top branches, highest-rated products, and peak sales periods.
- **Customer Insights**: Demographics, preferences, and buying trends.
