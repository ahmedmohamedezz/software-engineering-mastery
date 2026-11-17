# Advanced DB Features

## Sub Queries

- sub-queries allow us to use `select` statments within other database query types to provide the contents of the `where`, and sometimes `having` clauses. So, we can apply more complex business rules

> always do as much of data manipulation at the db level. it's faster, and you may safe your network bandwidth

- we can use 4 operators with sub-queries (`IN`, `ALL`, `ANY`, `EXISTS`)

- example, suppose we want to get all products with price > avg price of all products

```sql
SELECT ProductName, ProductPrice
FROM Product
WHERE ProductPrice > (SELECT AVG(ProductPrice) FROM Product)
```

> note that the result of the sub-query won't change for different rows of the outer query

### Correlated Sub Queries

- correlated sub-queries allow us to write sub-queries that relies on 1/more of the ourter query values. This means that for every row in the outer query, the inner query may return a **different result**

- example, suppose we want to get all products with price > avg price of all products in their category

```sql
SELECT OuterProduct.ProductName, OuterProduct.ProductPrice
FROM Product OuterProduct
WHERE OuterProduct.ProductPrice > (SELECT AVG(ProductPrice) FROM Product WHERE ProductCategory = OuterProduct.ProductCategory)
```

### IN, ANY, ALL, EXISTS

- let's see examples on using the mentioned operators with sub-queries

- Q1, find all managers in the systems (managerId = null, or employee.Id = someOtherEmployee.managerId)

```sql
SELECT EmployeesOuter.EmployeeName
FROM Employees EmployeesOuter
WHERE (Manager IS NULL) OR EmployeesOuter.EmployeeId IN (
        SELECT Employees.Manager FROM Employees
        WHERE Employees.Manager = EmployeesOuter.EmployeeID
    );
```

- we can re-write the same query using `EXISTS` (prefered, as it more readable and may guide the optimizer with a hint)

- usually modern optimizers tread `IN`, `EXISTS` the same way

## Views

- `view`: virtual table that is made up of another `select` statement. once created, it could be used like any other table in the DB

- benifits of using views:
  - they re-use the pre-compiled query plan when executed multiple times
  - used to restrict access to some rows/columns of the underlying tables
  - used to abstract a complex query, or build a summary table

---

## Readings

- how DB optimize performance for the `IN` operator
  - what if the column is indexed ? `id IN (....)`. is it useful for the DB to sort the `IN` list ?
  - what if not ? would the DB build an in-memory hash table from the `IN` list?

- `LEFT SEMI JOIN`, `HASH JOIN`

- Sub query un-nesting, OR expansion
