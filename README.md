# SQL-Footnote

# Superstore Dataset Analysis

To help Superstore better understand their data, I wrote SQL queries to answer the following questions:

## Question 1: Products Made by Xerox
```sql
SELECT DISTINCT(sub_category)
FROM products
WHERE product_name ILIKE '%xerox%';
```
**Answer:** Paper and Machines

**SQL Skills Gained:**
- Use of `DISTINCT` keyword.
- String pattern matching with `ILIKE`.

## Question 2: Countries in Western Europe Sub-region
```sql
SELECT COUNT(DISTINCT country)
FROM regions
WHERE sub_region = 'Western Europe';
```
**Answer:** 6

**SQL Skills Gained:**
- Aggregate functions with `COUNT`.
- Filtering using `WHERE` clause.

## Question 3: Customers in Consumer Segment with Names Starting with "S"
```sql
SELECT COUNT(DISTINCT customer_name)
FROM customers
WHERE customer_name LIKE 'S%' AND segment = 'Consumer';
```
**Answer:** 35

**SQL Skills Gained:**
- Combining conditions with `AND`.
- String pattern matching with `LIKE`.

## Question 4: Total Number of Returned Items with Given Reasons
```sql
SELECT SUM(return_quantity)
FROM returns
WHERE reason_returned != 'Not Given';
```
**Answer:** 31,781

**SQL Skills Gained:**
- Aggregate functions with `SUM`.
- Filtering based on conditions.

## Question 5: Orders with Standard Shipping and Discount Code
```sql
SELECT COUNT(DISTINCT order_id)
FROM orders
WHERE ship_mode = 'Standard Class' AND discount > 0;
```
**Answer:** 385,615

**SQL Skills Gained:**
- Combining conditions with `AND`.
- Handling numeric conditions with `>`.

```

These queries demonstrate my proficiency in SQL, including the use of aggregate functions, filtering, and string pattern matching.
```

# SQL Notes - Section 2

## 6. Do our customers prefer a certain type of shipping class? Find the number of orders per ship mode.

```sql
SELECT ship_mode, COUNT(order_id) AS count_of_orders
FROM orders
GROUP BY ship_mode;
```

### Answer:
- "First Class": 51,911
- "Same Day": 204,627
- "Second Class": 333,013
- "Standard Class": 401,126

**Knowledge/Skills Gained:**
- Aggregate functions like COUNT()
- GROUP BY clause for grouping data
- Understanding of different shipping modes

## 7. How many unique salespeople do we have employed in each region? How does that compare to the number of unique countries in each region?

```sql
SELECT region, COUNT(DISTINCT salesperson) AS count_of_unique_salespeople, COUNT(DISTINCT country) AS count_of_unique_countries
FROM regions
GROUP BY region;
```

### Answer:
- "Americas": 9 salespeople, 26 countries
- "APAC": 5 salespeople, 29 countries
- "EMEA": 5 salespeople, 91 countries

**Knowledge/Skills Gained:**
- DISTINCT keyword for counting unique values
- Comparison of unique values between columns

## 8. Find the most popular reason for returns.

```sql
SELECT reason_returned, COUNT(reason_returned) AS count_of_reason_returned
FROM returns
GROUP BY reason_returned
ORDER BY count_of_reason_returned DESC
LIMIT 1;
```

### Answer:
- "Not Given": 18,988

**Knowledge/Skills Gained:**
- ORDER BY and LIMIT clauses
- Identifying and handling missing values

## 9. Bonus: Create a query that groups the total number of products available by vendor.

```sql
SELECT
  CASE
    WHEN product_name ILIKE '%3M%' THEN '3M'
    WHEN product_name ILIKE '%Apple%' THEN 'Apple'
    -- ... (similar WHEN clauses for other vendors)
    ELSE 'Other'
  END AS vendor_group,
  COUNT(*) AS product_count
FROM products
GROUP BY vendor_group;
```

### Answer:
- "Avery": 302
- "Apple": 111
- "Samsung": 120
- "Panasonic": 107
- "Other": 8,824
- "Xerox": 260
- "Epson": 88
- "Cisco": 119
- "Hewlett-Packard": 214
- "Logitech": 141
- "3M": 6

**Knowledge/Skills Gained:**
- Using CASE statements for conditional grouping
- Handling vendor details in a large dataset


# SQL Notes - Section 3

## 10. Use JOINs and UNIONs to answer the following questions:

### 10.1 Which region saw the most returned items? For what reasons?

```sql
WITH ReturnCounts AS (
  SELECT rg.region, r.reason_returned, COUNT(*) AS count_per_reason_returned
  FROM orders AS o
  JOIN Returns AS r ON o.order_id = r.order_id
  JOIN Regions AS rg ON o.region_id = rg.region_id
  GROUP BY rg.region, r.reason_returned
)

SELECT region, reason_returned, count_per_reason_returned
FROM ReturnCounts
WHERE (region, count_per_reason_returned) IN (
  SELECT region, MAX(count_per_reason_returned)
  FROM ReturnCounts
  GROUP BY region
)
ORDER BY count_per_reason_returned DESC;
```

### Answer:
- "Americas": "Not Given" - 8004
- "EMEA": "Not Given" - 7034
- "APAC": "Not Given" - 4398

**Knowledge/Skills Gained:**
- Common Table Expressions (CTEs)
- Subqueries in SELECT and WHERE clauses

## 11. What product was returned most often?

```sql
SELECT rg.region, r.reason_returned, COUNT(*) AS count_per_reason_returned
FROM orders AS o
JOIN Returns AS r ON o.order_id = r.order_id
JOIN Regions AS rg ON o.region_id = rg.region_id
GROUP BY rg.region, r.reason_returned
HAVING COUNT(*) = (
  SELECT MAX(return_count)
  FROM (
    SELECT rg.region, COUNT(*) AS return_count
    FROM orders AS o
    JOIN Returns AS r ON o.order_id = r.order_id
    JOIN Regions AS rg ON o.region_id = rg.region_id
    GROUP BY rg.region
  ) AS MaxReturnCount
)
ORDER BY count_per_reason_returned DESC;
```

### Answer:
- "Staples" - 281

**Knowledge/Skills Gained:**
- More advanced JOINs and HAVING clause

## 12. Which of our “top vendors” saw the most returns?

```sql
SELECT vendor, COUNT(*) AS return_counts
FROM (
  SELECT
    CASE 
      WHEN product_name ILIKE '%3M%' THEN '3M'
      WHEN product_name ILIKE '%Apple%' THEN 'Apple'
      -- ... (similar WHEN clauses for other vendors)
    END AS vendor
  FROM products
  JOIN orders ON orders.product_id = products.product_id
  JOIN returns ON orders.order_id = returns.order_id
) AS categorized_products
GROUP BY vendor
HAVING vendor IS NOT NULL
ORDER BY return_counts DESC;
```

### Answer:
- "Avery" - 1896
- "Xerox" - 1197
- "Logitech" - 765
- "Samsung" - 703
- "Hewlett-Packard" - 601
- "Apple" - 547
- "Cisco" - 442
- "Panasonic" - 311
- "Epson" - 307
- "3M" - 55

**Knowledge/Skills Gained:**
- Extensive use of CASE statements in JOINs
- Handling multiple conditions in SQL

## 13. Which product is most profitable with the consumer segment?

```sql
SELECT o.product_id, product_name, SUM(profit) AS total_profit
FROM orders AS o
JOIN products AS p ON o.product_id = p.product_id
JOIN customers AS c ON o.customer_id = c.customer_id
WHERE segment = 'Consumer'
GROUP BY o.product_id, product_name
ORDER BY total_profit DESC
LIMIT 1;
```

### Answer:
- "TEC-CO-10004722" - "Canon imageCLASS 2200 Advanced Copier" - $12,904.17

**Knowledge/Skills Gained:**
- Aggregating and summing data
- Using GROUP BY and ORDER BY in conjunction


# SQL Notes - Section 4

## 14. Use subqueries to answer the following questions:

### 14.1 Has our organization sales grown over the years?

```sql
SELECT DATE_PART('year', order_month) AS order_year, AVG(monthly_sales) AS monthly_average_sales_per_year
FROM (
    SELECT DATE_TRUNC('month', order_date) AS order_month, SUM(sales) AS monthly_sales
    FROM orders
    GROUP BY order_month
) AS fahmi
GROUP BY order_year;
```

#### Answer:
```
2015 - 2114.06
2016 - 789018.3975
2017 - 2535652.5183
2018 - 5688918.4175
2019 - 10904337.575
2020 - 4447514.74
```

**Knowledge/Skills Gained:**
- Date functions in PostgreSQL
- Nested subqueries and aggregations

## 15. On average, what percent of salespeople make a sale each month?

```sql
WITH MonthlySalesCounts AS (
    SELECT
        DATE_TRUNC('month', order_date) AS order_month,
        COUNT(DISTINCT salesperson) AS salespeople_count
    FROM regions AS r
    INNER JOIN orders AS o ON r.region_id = o.region_id
    GROUP BY order_month
)
SELECT AVG(salespeople_count * 100.0 / total_salespeople) AS average_percent_salespeople_per_month
FROM MonthlySalesCounts
CROSS JOIN (
    SELECT COUNT(DISTINCT salesperson) AS total_salespeople
    FROM regions AS r
    INNER JOIN orders AS o ON r.region_id = o.region_id
) AS TotalSalespeople;
```

#### Answer:
```
99.2
```

**Knowledge/Skills Gained:**
- Common Table Expressions (CTEs)
- Percentage calculations in SQL

## 16. What percent of all sales in the United States did returns make up in 2020?

```sql
SELECT (
    SELECT SUM(o.sales) AS return_total_sales
    FROM orders o
    JOIN regions g ON o.region_id = g.region_id
    WHERE o.order_id IN (
        SELECT order_id
        FROM public.returns
    )
    AND g.country = 'United States'
    AND DATE_PART('year', o.order_date) = 2020
) / SUM(sales) * 100 AS return_percent_sale_United_States
FROM orders o
JOIN regions g ON o.region_id = g.region_id
WHERE DATE_PART('year', order_date) = 2020
AND g.country = 'United States';
```

#### Answer:
```
8.32
```

**Knowledge/Skills Gained:**
- Subqueries in SELECT and WHERE clauses
- Advanced percentage calculations in SQL
```

