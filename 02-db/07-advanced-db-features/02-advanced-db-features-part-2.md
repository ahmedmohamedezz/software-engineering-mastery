# Advanced DB Features

## Stored Procedures & Triggers

- `stored procedure`: is a named, precompiled block of SQL statements (and control logic like IF/ELSE or loops) saved in the database
  - you invoke it explicitly—typically with `CALL` or `EXEC`, and you can pass input parameters and return output values or result sets.

- what stored proc. offer?
  - pre-compilation: unlike requests to db to execute queries that should be compiled everytime, and the planner needs to figure out a plan to execute it. stored proc. are compiled & planned once then executed directly when called
  - server-side constructs: stored proc. support looping and conditionals logic which is useful to simulate server side operations
  - security: since stored proc. are stored in the DB itself, the SQL statements are not sent over network, and thus they can't be sniffed
  - performance: for operations that require huge data processing to get a simple output. we want to avoid sending all that data as it takes considerable time

- example of when to use stored procedures
  - inserting orders in e-commerce system: requries updating many tables
  - checklng for an alarm state in time serles data: manipulates large volume of data
  - performing actions on remote databases: better performance when you only send the key elements to process rather than the process itself

## Physical DB Tunning

- in some Situations we can see improvements by optimizing the physical layer of the database ourselves

- first, we need to know about planners. and how the DB store, and manipulate our data. Then we'll look at ways for enhancing the performance such as indexing, or denormalization

### Query Plans

- for a single query, the DBMS has a lot of ways to execute it. sometimes, we want to pre-empt the guesses with some ideas of our own. this happens using:
  - explanation commands: ask RDBMS to explain how it would approach a query (e.x. `EXPLAIN`)
  - hint commands: give hints as how we would like to execute a query

> optimization is not considered when the data volume is low (100 row ?)

### Indexes

- indexes help us make faster reads, but slightly slower writes

- what to index ?
  - PK & FK columns
  - columns used in the `where` clause of `select`, and `update` statements

- what not to index ?
  - table that are written to more than they read (logging tables)
  - you can use a batch job that reconcile those logs later

#### Types of Indices

- mainly there are two types: clustered, and non-clustered indices

- `non-clustered`: has a structure that is completely separate from the table
  - you can define as many as you need
  - the key can be unique/not

- `clustered`: part of the table itself and sorts and stores the rows of data in a table based on the key of the index
  - one / table
  - usually used with range scans, so the index can find the range start, then fetch rows sequentially from disk (better than jumping to different disk locations for every record)

## Managing Concurrency

- concurrency is about the no. of users executing sql against your DB at a single point of time

### Locks

- locking involves placing a flag on a certain object to indicate that it's **in use**

- `Granularity` of locks determines the objects it affects
  - field
  - row
  - table
  - DB

- `Type` of lock determines what actions are available on the locked object (read-only vs. read-write)
  - shared: acquired when I need to read the object (only if it's not hold by other exclusive lock)
  - exclusive: acquired to update an object (only one exclusive lock at a time, and must wait for all shared locks to be released first)

> we usually don't set up locks ourselves, but the DB do it for us depending on the isolation level we use

### Transactions

- transactions tel/ the database that all of the commands we execute within that transaction must succeed or fail as a group

- why do we need it ? **consistency**
  - what if order is inserted but items not ? or if a report count 5 orders but summed only 4 totals ? _data inconsistency_

#### Transactions Properties

- 4 properties need to hold in order to define what a trans. is, or needs to be. represented by the acronym **ACID**, which stands for:

- `Atomic`: it must either all succeed or all fail

- `Consistent`: a transaction must always leave the database in a consistent state (internally like FK integrity, and externally satisfying business rules)

- `Isolated`: a transaction shouldn't be able to see **uncommitted results** of other ones. and other trans. can't see it's **uncommitted results**
  - this has a role in limiting the no. of concurrent users our apps support

- `Durable`: once committed, the results of a transaction must be permanently stored in the database
  - data won't be lost in case of a failure
  - if faultre happened while the trans. if working -> rollback
  - if after the trans. has been committed -> saved permanently

#### Isolation Levels

- isolation levels determines what data a transaction can see while it's running, ordered from the least isolated to the most as:
  - read uncommitted
  - read committed
  - repeatable read
  - serializable

- `Read Uncommitted`: lets transaction read any changed uncommitted data of another transaction

- useful to use, if we query the category hit-count in an e-commerce system (other trans. can update the count without any waiting / locking)

- however, it can also cause problems like **dirty reads** which describes reading data that maybe rolled back (not committed yet)

- e.x: a customer browse products, and decided to buy 1 that is currently being inserted (exist in products, but not in storage yet)
  - the product insertion is rolled back for some reason, but the client still have it in his cart

- `Read Committed`: a transction sees only the data that other transactions has committed. saves us from **dirty reads**, but still can fall in other problems

- e.x: suppose a client was transfering money to another account, the first time we checked the balance, there was enough money to complete the transfer.
  - before we withdraw the money, another automatic payment took place and now there is not enough money
  - this problem is called **non-repeatable read**, in which the same query can return different results over time
  - the ideal situation is to prevent other trans. from updating the data we're interested in

- `Repeatable Read`: solves the problern of data being changed by one transaction while in use by another transaction

- repeatable read locks the data it uses so that other transactions that wants to **modify** the data are forced to wait until the lock is released
  - what lock is used to prevent updates, but allow other reads ? **shared lock**

- **exercize**
  - assume we want to transfer money from Acc(A) to Acc(B). the transaction works in _repeatable read isolation level_
  - here is the flow assuming Acc(A) has a balance of a _100$_
  - Trx(A) runs `select balance from account where accountId = x`
  - Trx(B) runs `select balance from account where accountId = x`
  - Trx(B) runs `update account set balance = balance - 70 where accountId = x`
  - Trx(A) runs `update account set balance = balance - 70 where accountId = x`
  - which transaction will success in MVCC (PG) vs. Lock-based (SQL Server) environments

- so, repeatable read solves **dirty reads** and **non-repeatable read** problems, but what if instead of updating an existing row between 2 reads, a new row(s) got inserted.
  - the query is the same, the existing values are the same. but new rows are inserted, which can lead to different aggregations (SUM(3 rows), but AVG(5 rows))

- this problem is called **phantom inserts**

- this level can limit the **concurrent requests** to the same resource due to locking

- `Serializable`: asures that the result will be as if the trans. ran in series (one after another)

- this level solves the **phantom inserts** problem, but severely limit the concurrency of the database

- usually, use DB-wide, or table-wide locking

#### Notes

- always work with the lowest possible level, you can simulate some Trx. to see in which level you fit-in

- you can also start with serializable, and see if you can handle the disadvantages then go the next and so on

- example: `SET TRANSACTION ISOLATION LEVEL REPEATABLE READ`

### Deadlocks

- a deadlock occurs when two transactions are holding locks and are both trying to take out locks on the data that the other is holding

- in this situations, RDBMS chooses a winner, and a loser
  - loser is rolled back
  - winner acquires the lock and continue

> it's the responsibility of the app to implement retries for the loser

#### Tips to avoid many deadlocks

- use the lowest IL possible
- keep your trx. as short as possible (no. of commands executed & duration)
- access data in the same order in all transactions (Orders -> Products -> Categories)

---

## Readings

- stored procedures vs. triggers vs. functions

- `INSTEAD OF` triggers
