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

---

## Readings

- stored procedures vs. triggers vs. functions

- `INSTEAD OF` triggers
