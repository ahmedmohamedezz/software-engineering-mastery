# Implementing the DB

## Naming Convnetions

- it's important to agree with developers, and DBAs on a defined naming convnetion to use through out the system. it makes the code base consistent

- for tables, you can go with plural, or singular names (unless it's a reserved keyword like `Order`)
  - e.x. `Customer`, `Category`, `Products`, `Orders`

- the typing case for columns could be `CamelCase`, `snake_case`, `pascalCase`

- for columns, you can go with direct names `name`, or prefixed-names to avoid like-named columns in different tables `custName`

## Data Types and Nullability

- a good practice is to use standard data types to make database portable, and not tied to specific vendor

### Character Data Types

- `VARCHAR(N)`: text with max length, actual physical values are is long as the value they hold
  - comes with an overhead to be processed

- `CHAR(N)`: fixed length text, if assigned with value of `length < n`, the remaining space is filled with spaces (always stored of length n)
  - may waste space if you don't need `n` most of the time

- another types exist like `TEXT`, or `CLOB`

> VARCHAR is use more often that CHAR

---

## Numeric Data Types

- `SMALLINT`: 16-bit integer
- `INTEGER`: 32-bit integer
- `NUMBERIC(P, S)`: holds fractions. `P` is the precision (how many total digits), and `S` is the scale (how many digits after decimal point)
  - used to represent monetary columns
- `FLOAT`: for scietific apps
- `REAL`: for scietific apps

> specific vendors add their customizations like MySQL TINYINT, or SQLServer MONEY (holds currency information)

---

## Date Data Types

- `DATE`: represent date up to day precision (e.x. `2022-MAR-03`)
- `TIME`: represent time up to seconds precision (e.x. `18:59:59`)
- `DATETIME`: not in the standard. combines the previous 2

---

## Nullability

- `null` is used to represent absence of a value.

- some people think it's useless and use a value default fallback (0 for numeric, "" for strings, ...)

- using default values to represent absence of a value is a bad practice as they could be misleading if used by mistake in calculations

- whenever a value is optional, use `NULL` to express its absence

--

## Natural and Surrogate Primary Keys

- `PK`: 1/more columns that has 2 characteristics _unique_ and _not null_

- a natural key exists in a table by default `Employee.SSN` and could be used as a PK.

- while a surrogate key is added when no column in the table can serve as PK `id`

- such columns usaully are of type `INTERGER` with some support from DB systems (`SERIAL`, `IDENTITY`, `SEQUENCE`)

- most PK have initial value + uses an increment to calculate the next

> one thing to note is to determine what is the max values expected in this column to avoid passing the upper-limit of this data type

- if you're using a surrogate key, don't reveal it unless you need to

---

## Foreign Keys and Relational Integrity

- forign keys exist to introduce data consistency in our apps
  - ensure an order belongs to an existing customer

- PK always set on the 1 side of a relationship, while FK on the many side

- `referential integrity` is a concept that states that:
  - PK must exist before a forign key does
  - PK can't be removed as long as it's referenced by a FK

---

## Best Practices

- images are stored as URLs to it's real location (on cloud), rather than stored as bytes, or BLOB
  - this is a significant query performance increase, and safe to avoid error like `OutOfMemoryError` in java

- composite indices are search from left-to-right

- a good strategy is to define an index for any foreign key that isn't the first co/umn of a primary key

- numeric data is indexed much more efficiently than character data

- don't use `SELECT *` as it is considered a bad practice for the following reasons
  - no need to fetch all fields (more data to transmit -> bad performance, consumed network bandwidth)
  - may fetch sensitive data (tokens, passwords)
  - may break the code if columns are added/remove

- don't use `LIKE` when not necessary as regex comparisons degrade performance

---

## Readings

- why processing `VARCHAR` rows are more costly than `CHAR` ones?

- when can FK be null?
