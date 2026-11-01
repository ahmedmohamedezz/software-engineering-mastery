# Data Manipulation Language (DML)

## Reading Data (SELECT)

- look at the following examples

```sql
-- most simple
SELECT <columns> -- or * for all columns
FROM <tables>
[WHERE <conditions>];

-- where conditions examples
WHERE age = 20;
WHERE age > 20;
WHERE age != 20;        -- same as WHERE age <> 20;
WHERE age BETWEEN 20 AND 30;
WHERE age >= 20 AND age <= 30;
WHERE age IN (20, 30);  -- same as WHERE age = 20 OR age = 30;
```

- but what if the data we need resides in several tables ?

- in the following example, we were expecting only 2 rows, but got more than this. why ?

```sql
SELECT SaleNo, SaleDate, Amount, Salesrep
FROM CUSTOMER, SALE
WHERE First = 'Amelia' AND Last= 'Waverley';
```

- the default behavior is to get all possible combinations of rows from the tables. this is called a **cartesian product**.

- any row with that name (first, and last) (not necessarily the same person) will be included in the result

## Joins

- a join operation combines rows from 2 tables in some way

- a join can be done in several ways:
  - natural join
  - condition-based join
  - outer join

### Natural Join

- the simplest form of a join, used when we depend on the columns having the same name in both tables

- a natural join doesn't have a `ON`, or `USING` clause

```sql
SELECT SaleNo, SaleDate, Amount, Salesrep
FROM CUSTOMER NATURAL JOIN SALE
WHERE First = 'Amelia' AND Last= 'Waverley';
```

### Condition-based Join

- a join that uses a boolean condition to determine how to join the tables

- see the pre SQL-92 standard and SQL-92 standard conditional join (using the `WHERE` clause vs. using the `ON` clause)

> note that we use the table names (or aliases) to distinguish between columns with the same name in both tables

```sql
-- pre SQL-92 standard
SELECT SaleNo, SaleDate, Amount, Salesrep
FROM CUSTOMER, SALE
WHERE 
    CUSTOMER.CustomerNo = SALE.CustomerNo
AND 
    First = 'Amelia' AND Last = 'Waverley'; 

-- SQL-92 standard
SELECT SaleNo, SaleDate, Amount, Salesrep
FROM CUSTOMER JOIN SALE ON CUSTOMER.CustomerNo = SALE.CustomerNo
WHERE First = 'Amelia' AND Last = 'Waverley'; 
```

> note that inner join can repeat columns of the first table

```sql
SELECT ProductNo, ProductName, SoldAt
FROM PRODUCT JOIN SALE ON PRODUCT.ProductNo = SALE.ProductNo;

-- 1, macbook, store 1
-- 1, macbook, store 1
-- 1, macbook, store 2
-- 1, macbook, store 3 
```

### Outer Joins

- `Left Outer Join`: returns all rows from the left table, and the matching rows from the right table or `NULL` if there is no match

- `Right Outer Join`: returns all rows from the right table, and the matching rows from the left table or `NULL` if there is no match

- `Full Outer Join`: returns all rows from both tables, or `NULL` if there is no match

> the `OUTER` keyword is optional

```sql
SELECT ProductNo, ProductName, SoldAt
FROM PRODUCT LEFT OUTER JOIN SALE  -- same as LEFT JOIN
    ON PRODUCT.ProductNo = SALE.ProductNo;
```

---

### Note on Joins

- when working in the some environments, we do really care about the performance of our queries. this means that we usually don't want to use joins alot (especially with large tables)

- we can avoid this sometimes by denormalizing our tables

- suppose a customer wants to view his orders history. the simplest way for us is to join the `Customer`, `Product`, and `Sale` tables to get the answer. but we have millions of orders for millions of users, so this is not a good idea

- since the `Sale` data is almost static after an order is shipped. an alternative approach is to denormalize the mentioned data

```sql
CREATE TABLE SALE_HISTORY (
    -- customer data
    -- sale data
    -- product data
)

INSERT INTO SALE_HISTORY (C1, C2, ....)
SELECT (C1, C2, ...) FROM CUSTOMER JOIN SALE 
    ON CUSTOMER.CustomerNo = SALE.CustomerNo
JOIN PRODUCT
    ON SALE.ProductNo = PRODUCT.ProductNo;
```

- now, all we need is to refresh the `SALE_HISTORY` table on a regular basis using a `TRIGGER`. or as a `MATERIALIZED VIEW`

> this technique could be used with data that change frequently ONLY if the users doesn't require data to be absolutely accurate

---

### Readings

- why `SELECT *` is considered a bad practice ?
