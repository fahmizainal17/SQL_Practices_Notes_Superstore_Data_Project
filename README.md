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


