# Data Definition Language (DDL)

## Creation and Deletion of Tables

- creation of tables follow this syntax:

```sql
CREATE TABLE TABLE_NAME (
    COLUMN_NAME_1 DATATYPE_1 [DEFAULT DEFAULT_VALUE] [constraints],
    -- .....
    [constraints],
);
```

- note that NUMERIC(6, 2) means total of 6 digits, with 2 digits after the decimal point XXXX.YY

> in BOOLEAN columns, avoid null values

- you can give constraints a name, or the DB will generate one for you

- constraint names are useful later if you want to `ALTER` them

```sql
CreditLimit SMALLINT CONSTRAINT Customer_Constraint_CreditLimit_NonNegative CHECK (CreditLimit >= 0),
```

- for monetary values, use `NUMERIC`, or `INTEGER` instead of `FLOAT` to avoid rounding problems in money values

- here are the Sale table from previous chapter

```sql
CREATE TABLE SALE (
  SaleNo       SMALLINT,
  SaleDate     DATE            NOT NULL,
  CustomerNo   CHAR(9),
  ProductNo    CHAR(9)         NOT NULL,
  Qty          INTEGER         NOT NULL CHECK (Qty > 0),
  Amount       NUMERIC(6, 2)   NOT NULL CHECK (Amount >= 0),
  Salesrep     VARCHAR(50),

  PRIMARY KEY (SaleNo),
  FOREIGN KEY (CustomerNo)     REFERENCES CUSTOMER,
  FOREIGN KEY (ProductNo)      REFERENCES PRODUCT
);
```

- to drop the table, simply run `DROP TABLE SALE`. this will remove all table along with the data, indexes, triggers, etc.

> always make sure columns have descriptive names, and make sure that columns in different tables the represent the same thing have the same name

## Modification of Tables

- `ALTER TABLE` is used to modify existing tables, it's used in 3 ways:
  - `ADD`
  - `ALTER`
  - `DROP`

### ADD

- you can't add a not null column without a default value

```sql
-- add column
ALTER TABLE EMPLOYEE ADD COLUMN ManagerId DEFAULT 0 NOT NULL INTEGER;

-- add constraint
ALTER TABLE EMPLOYEE ADD CONSTRAINT FK_MANAGER FOREIGN KEY (ManagerId) REFERENCES EMPLOYEE; 
```

### ALTER

- used to add/remove default values from columns

```sql
ALTER TABLE EMPLOYEE ALTER COLUMN ManagerId DEFAULT 0 NOT NULL INTEGER;

ALTER TABLE EMPLOYEE ALTER COLUMN ManagerId SET DEFAULT 0;

ALTER TABLE EMPLOYEE ALTER COLUMN ManagerId DROP DEFAULT;

ALTER TABLE EMPLOYEE ALTER COLUMN ManagerId TYPE SERIAL;  -- change data type

ALTER TABLE EMPLOYEE ALTER COLUMN ManagerId SET NOT NULL;

ALTER TABLE EMPLOYEE 
    ALTER COLUMN ManagerId SET NOT NULL,
    ALTER COLUMN ManagerId SET DEFAULT 0;
```

### DROP

- to drop a column / constraint

```sql
ALTER TABLE EMPLOYEE DROP [COLUMN] ManagerId;
ALTER TABLE EMPLOYEE DROP CONSTRAINT FK_ManagerId;
```

---

## INDEXES

- creation and deletion of indexes follow this syntax:

```sql
CREATE [UNIQUE] INDEX INDEX_NAME ON TABLE_NAME (COLUMN_NAME_1, COLUMN_NAME_2, ...);
-- CREATE UNIQUE INDEX EmployeeiDindex ON EMPLOYEE (EmployeeiD); 

DROP INDEX INDEX_NAME;
-- DROP INDEX EmployeeiDindex;
```

- many DBMS automatically add unique indices on PKs
