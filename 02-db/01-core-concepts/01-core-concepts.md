# DB core concepts

- what's a data model ?
  - formalized method of structuring and manipulating data
  - structured description of real-world entities

- what's a database ?
  - structured and organized collection of data

- what's a DBMS ?
  - sw that manipulates DBs (create, modify, delete, query, etc.)

- why do we need NULLs?
  - to represent absence of a value, becaues mark values (e.x. 0, -1) are not always applicable (can't satisfy all domains)

- why do we need PK?
  - to act as a row identifier, since all other attributes may repeat
  - could be combination of 1/more columns
  - a table can have many alternate/candidate keys
  - it maybe one the entity attrs, of defined as a new surrogate key

- allowed values of a column are called **domain**

- why do we need FK?
  - to establish a relationships tables

---

## Normalization

- why can't we just put all the data in a single table?
  - it won't help speeding up queries (at a point)
  - it wastes space, since data will be repeated across multiple rows
  - how about update ? update multiple rows (duplication)
  - how about delete ? we may delete un-related data (delete customer address with the order ?)

- normalization helps us avoiding data insertion & deletion anomalies

- we have 5 NF, each one build on the previous one

- any table is be default in the 1st NF

- normalization & de-normalization (reverse process) depends on what you do with the data. if you're working with data that is mostly read-only, you may reduce the normalization level

- while normaliztion solves some data problems. it can affect performance, because the dbms will have to re-assemble the data to query it. on the other hand if you're working with mission-critical data (financial), you may want to keep it normalized and reduce redundancy to keep it consistent

### 1st Normal Form (1NF)

- states that none of the columns have multiple values (must be atomic/scalar), and no repeating-groups (set of related columns that repeat together)

look at the following table

```text
ALL_SALES
(
  **ProductNo**, Description, ReorderLevel, Price, QtyinStock, 
  CustomerNo, First, Last, Address, CreditLimit, 
  **SaleNo**, SaleDate, **ProductNo**, Qty, Amount, Salesrep
)
```

- what should be the PK of the previous table ?
  - `ProductNo, SaleNo, CustomerNo`: this triplet is unique / row

### 2nd Normal Form (2NF)

- states that no non-key attributes are **partially dependent** on the key
  - all columns are determined by the whole PK

- it may seem that table in 2nd normal form can't have composite PK, but it's not true in all the cases

- we notice that product description, stock, price doesn't depend on the key `(ProductNo, SaleNo, CustomerNo)`.

- what is functional dependency `C1 -> (C2, C3)`?
  - it means that if we know the value of some column (PK), we can easily know the values of other columns
  - it's a constraint between 2 attribute sets (X uniquely determines Y) `X -> Y` that states that no 2 rows that have the same `X` can have different `Y's`

```text
Product (**ProductNo**, Description, ReorderLevel, Price, QtyinStock)
Customer (**CustomerNo**, First, Last, Address, CreditLimit)
Sales (**SaleNo**, SaleDate, CustomerNo, ProductNo, Qty, Amount, Salesrep)
```

### 3rd Normal Form (3NF)

- let's assume that this is the product table

```text
PRODUCT (**ProductNo**, Description, ReorderLevel, Price, QtyinStock, SupplierCode,
SupplierName, SupplierAddress) 
```

- a table is in 3NF if it has no **transitive dependency**, that is no column is determied by non-key column

``` text
PRODUCT (**ProductNo**, Description, ReorderLevel, Price, QtyinStock, SupplierCode)
SUPPLIER (**SupplierCode**, SupplierName, SupplierAddress) 
```

### 4th Normal Form (4NF)

- suppose that customers can have more than 1 address, we can't just add another column because we don't know how many addresses a customer has. and we can't have multiple rows (one for each address, repeating the other columns) because then the PK won't determine a single a value of each colulmn, it determines a _collection of values of a single column_ which is called **multi-valued dependency**

- a table is in 4NF if the primary key determines only single vatues of each column, not
collections of vatues (that is, there are no multi-vatued dependencies).

---

## Integrity

- data integrity means that data in the tables should have valid values and are consistent across the tables

- integrity is a concept that applies to any database regardless of data model

- could be built-in (PK must be unique), or user-defined (user gender must not be NULL, and must be either 'f', 'm')

- relational DB model has 3 built-in integrity rules:
  - entity integrity: PK are unique and not null
  - referential integrity: FK must refer to an existing entity in another table, or it must be NULL
  - domain integrity: values must be within the domain of the column

- the DBMS store metadata of tables in speical area called **catalog**, or **data-dictionary**

- what that metadata could be?
  - table definition (name, no. of col., PK)
  - columns of the table
  - constraints (PK, FK, domain)
  - user info (username, password)
  - access privilages
  - index details
  - statistics (size, avg no. of queries, etc.)

---

## Physical data access methods

- database performance is determined at the physical storage level

- it's important the the physical model is separated/independent from the logical model which make it easy to apply enhancements without changing the logical model (affecting the users)
  - aka **physical data independence**

- accessing a data that is in RAM (main memory) is much faster than accessing a disk (slow)
  - few NS compared to set of MS

- we can't depend on the HW getting faster overtime, but what we can do is to design the DB to be as efficient as possible

- one way is that we can cache the data we read from disk, so we can reduce the number of disk accesses

- how the data is stored ?
  - in files, each is divied up into set of blocks (aka pages)

- a page is the smallest unit of data that can be read-from/ write-to disk

- how the data is retrieved from disk ? (how to find/access + how to read)

- we have 3 ways to access data:
  - sequential access: start from the beginning, and go one-by-one to the end (table scan)
  - indexed access: search using separate structure called an **index** that splits the values in a hierarchical way
  - direct access: the fastest, often using **hash indexes** (hash tables)

- usually indexes are implemented using a **B(+)-trees**

- the index contains the values of a key (set of keys) sorted in a specific order
  - the keys could be unique, or not

- if the index is small enough, it maybe loaded in memory. which leads to 1 disk access (approx.) to find a single record

> note that even that the keys are sorted, it doesn't mean that the underlying records are sorted.

- indexes comes with a trade-off, having many keys that are inserted/updated/deleted. we need to re-balance the index structure to keep it consistent

- when you're adding indexes, you should think about operations that can occur on that data

- best case access when the index can fit in memery is `1` disk access to get the record

- worst case access when the index can'y fit in memery is `1` disk access/index node + `1`to get the record

- hashing indexes return a physical address given a key, so needs to distribute the rows evenly on it's hash space, and no guarantee that records hashes will be adjacent

- hash indexes are bad for range queries, as records are usually in a different block

---

## Readings

- difference between b-tree vs. b(+)-tree
