# Data Manipulation Language (DML)

## Creating Data (INSERT)

- look at the following examples

- it's not necessary to specify all columns (some are generated automatically, some have default values)

```sql
-- format: INSERT INTO table_name (colums) VALUES (values);

INSERT INTO EMPLOYEE 
    (EmpNo, First, Last, Address, CreditLimit) VALUES
    (1, 'John', 'Doe', '123 Main St.', 1000.00);


-- EmpNo is generated automatically
INSERT INTO EMPLOYEE 
    (First, Last, Address, CreditLimit) VALUES
    ('John', 'Doe', '123 Main St.', NULL);

-- add multiple rows at once
INSERT INTO EMPLOYEE 
    (EmpNo, First, Last, Address, CreditLimit) VALUES
    (1, 'John', 'Doe', '123 Main St.', 1000.00),
    (2, 'Jane', 'Doe', '123 Main St.', 2000.00),
    (3, 'John', 'Smith', '123 Main St.', 3000.00);
```

## Updating Data (UPDATE)

- look at the following examples

- if no `WHERE` clause is specified, all rows will be updated

```sql
-- format: UPDATE table_name SET column_name = new_value WHERE condition;

UPDATE EMPLOYEE
SET CreditLimit = 1000.00, Address = '123 Main St.'
WHERE EmpNo = 1;

-- update multiple rows at once
UPDATE EMPLOYEE SET CreditLimit = 1000.00 WHERE EmpNo IN (1, 2, 3);
```

## Deleting Data (DELETE)

- look at the following examples

- if no `WHERE` clause is specified, all rows will be deleted

- you can't delete a row that is referenced by another table using a foreign key (break referential integrity)

```sql
-- format: DELETE FROM table_name [WHERE condition];

DELETE FROM EMPLOYEE WHERE EmpNo = 1;

-- remove all rows
DELETE FROM EMPLOYEE;

-- this will break this product is a FK in another table
DELETE FROM PRODUCT WHERE ProductId = 1;
```

- if we want to remove all rows of a table, and we don't care about the data, we can use the `TRUNCATE` command

- `TRUNCATE` is faster than `DELETE`, because it doesn't log the rows deleted (drop + re-create)

---

### Readings

- `DELETE` vs. `TRUNCATE`
  - delete is DML, truncate is DDL
  - delete remove rows one-by-one, and logs the deletions
  - truncate removes all rows at once, and doesn't log the deletions (drop + re-create)
  - truncate doesn't support `WHERE` clause
