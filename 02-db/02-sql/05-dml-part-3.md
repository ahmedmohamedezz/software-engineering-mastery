# Data Manipulation Language (DML)

## Reporting Features

- the select command supports grouping, sorting, and aggregation functions

- here are another form of the `SELECT` command

```sql
SELECT column_name_1, column_name_2, ...
FROM table_name
WHERE condition
GROUP BY column_name_1, column_name_2, ...
HAVING condition
ORDER BY column_name_1, column_name_2, ...
LIMIT number;
```

- `ORDER BY` works in 2 ways:
  - `ASC`: ascending order (default)
  - `DESC`: descending order

- `LIMIT` is used to limit the number of rows returned (top N)

- use `SELECT DISTINCT` to remove duplicate rows

- there are basic 5 aggregation functions:
  - `COUNT`, `SUM`, `MIN`, `MAX`, `AVG`

- because aggregates return a single value, we can't mix them with normal columns unless we use a `GROUP BY` clause

- see the following examples

```sql
SELECT COUNT(*)
FROM CUSTOMER;

SELECT COUNT(DISTINCT Position)
FROM EMPLOYEE;

SELECT SUM(Product.Price * Sale.Qty)
FROM SALE, PRODUCT
WHERE SALE.ProductNo = PRODUCT.ProductNo;
```

> all aggregate functions ignore `NULL` values by default except `COUNT`

- `COUNT(*)` counts all rows (including `NULL`s). while `COUNT(column)`, or `COUNT(DISTINCT column)`, counts non-`NULL` rows

- to group rows that have some values in 1/more columns, use `GROUP BY`

```sql
-- find out where each position could exist
SELECT EMPLOYEE.Position, EMPLOYEE.Location
FROM EMPLOYEE
GROUP BY EMPLOYEE.Position, EMPLOYEE.Location;
```

- what about when we need to filter an aggregate (rows with sum > 1000) ? use `HAVING` clause, as `WHERE` clause doesn't work with aggregate functions

## Useful Utilities

- look at the following queries

> `AS` keyword is optional

```sql
-- select expressions
SELECT Product.Qty * Product.Price
SELECT Customer.Name, ' has bought ' || Sale.Qty || ' ' || Product.Name

-- renaming columns & tables
SELECT P.QTY as Quantity
FROM PRODUCT P;

-- self join + table renaming
SELECT EMP.Name, MGR.Name
FROM EMPLOYEE BMP JOIN EMPLOYEB MGR
    ON EMP.ManageriD = MGR.EmployeeiD;
```

## Set Operations

- many RDBMS support set operations such as `UNION`, `INTERSECT`, and `EXCEPT`

- they are used to combine sets of rows that has the same structure (same no. of columns + same data types)
  - varchar and char are considered the same data type

## Techniques for Building Select Queries

- to understand what a `SELECT` query does, do the following:
  - read the `from` clause to know which tables are involved
  - read the `where` clause to know which rows are selected
  - read the `select` clause to know which columns are retrieved

> when working with DB scripts (triggers, functions, specific data types like JSONB...), try to minimize vendor specific code to avoid portability issues

### Steps

- once you know the tables involved, and how they will be joined, you can build the `from` clause

- figure out the criteria that determine which rows are selected, and form them in boolean expressions and build the `where` clause

- next decide if we need to `group by`, or apply an `aggregate` function. then, if we need to filter out those columns, add the `having` clause

- finally decide what columns you want in the output, and build the `select` clause. and the order of them to build the `order by` clause

---

## Readings

- what's a stored procdure ?
  - collection of SQL statments along with some logic (loops, conditionals, etc.) that is stored directly inside the DB server and executed on demand
  - can accept parameters (in / out)

- what are the pros & cons of using stored procedures ?

- pros:
  - reduced network trafic, we only send the params. not the complex queries
  - access control
  - centralized logic (code reuse & instant updates for all apps)

- cons:
  - harder to version control and test
  - ties logic to DB implementation (PG is not the same as MySQL)
  - DBs can't easily scale horizontally. so during a load, it's better to use serveral app instances each does simple atomic queries rather than overload the DB server with huge complex queries

- compare using 'Y', 'N' for boolean columns vs. `1`, `0`

- compare soft delete vs. hard delete
  - cascading deletes ?
  - do we need the data ?

- when to use composite PK, or introduce a new simple PK ?

- what is CQRS ?
