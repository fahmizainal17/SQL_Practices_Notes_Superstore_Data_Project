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
