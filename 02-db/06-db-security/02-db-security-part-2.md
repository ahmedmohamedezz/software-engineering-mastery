# DB Security

## How to Secure Your Database

- start by defining what DBs are visible to a _login_, thin within each you can define table, view, procedure level permissions

### Table Level Permissions

> you may have the customers-related info in a separate DB to force different logins and permissions to such data

- some DBs lets you apply permissions on certain columns within a table

- types of permissions a login can have on a table:
  - select, insert, update, delete
  - DRI (declarative referential integrity) allowing to create/drop constraints (e.x. FK)

### Stored Procedure Level Permissions

- a stored procedure is a collection of SQL statements complied into a single object under a single name in a DB

- types of permissions:
  - create
  - drop

### View Level Permissions

> most views are created as read-only

- views are like a virtual table that can return data to users

- types of permissions:
  - select, insert, update, delete

- if a hacker gain access to update a view, he can modify the data in more than one table at one time

## Setting Permissions

- it maybe better to grant permissions to objects as you create them

- see the following examples on a login called `MyLogin`

```sql
-- for setting
GRANT SELECT, INSERT, UPDATE ON MyTable TO MyLogin;

GRANT EXECUTE ON MyProcedure TO MyLogin;

GRANT SELECT ON MyView TO MyLogin;

-- for revoking
REVOKE SELECT, INSERT, UPDATE ON MyTable FROM MyLogin;

REVOKE EXECUTE ON MyProcedure FROM MyLogin;

REVOKE SELECT ON MyView FROM MyLogin;
```

> only a few people should know the creds of the DB admin account

## Inline SQL vs. Stored Procedures

- let us discuss the dangers of inline sql, and stored prodcedures. what are they? how to avoid it?

### Inline SQL

- the most common danger is SQL injection

- `SQL injection`: is a term used to describe the insertion of extra SQL statements into an inline SQL stream by manipulating the data input of the application

- anothe common danger is the lack of error handling that can expose the structure of the DB

- example

```java
// The application builds this query dynamically using what the user typed (input)
String query = 
    "SELECT * FROM users WHERE username = '" + USER_INPUT + "' AND password = '" + PASSWORD_INPUT + "';";

executeQuery(query);
```

- if the user types `admin'; drop table users --` as a username, the query will be:

```sql
SELECT * FROM users WHERE username = 'admin'; drop table users -- AND password = '';
```

- the previous statement may simple drop the users table, preventing any system user to access the web app

- another common trick is to write `admin' OR '1' = '1`. selecting all data from the users table

#### how to avoid SQL injection?

1. a login should never have administrative permissions (read-only account can't update/delete data)

2. we should validate the expected data-type and length of the imcoming data

3. we should filter/escape quotes (and other suspicious characters like semicolons, or sql keywords) in the data before passing it to the DB

4. we should have a proper error handling in case of a failure (never expose your stacktrace, or DB exceptions)

> returning friendly error messages to users is a good idea, but don't forget to log the real issues so you can fix them later (logging)

### Stored Procedures

> as a best practice you should always use stored procedures instead of inline SQL whenever possible

- why ?
  - stored procedures are more secure than inline SQL
  - execute faster
  - less data transferred over the network (could be sniffed)

> stored procedures are not immune to SQL injection attacks

- a best practice is to use a global error handler to log the real issues and return friendly error messages to users when something unexpected happens

## Using Views to Restrict Data Access

- views let's you provide row/column level permissions to users

- we can prevent a login from a accessing a table, and grant it to a view that selects some columns from that table

- a best practis is to define logins with read-only access to DB. along with views to select the data
