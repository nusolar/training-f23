# Basics

## SQL statements
Operations on an SQL database are done using SQL statements. The three basic
statements you'll need to know are `CREATE TABLE`, `INSERT`, and `SELECT`.

> ### SQL quirks
> There are a few quirks of SQL that you should be aware of:
> * SQL keywords in almost all database engines are case-insensitive, but it
> is common practice to write them in uppercase.
> * SQL statements end with semicolons. When you interact with a database
> through a library like `sqlite3`, they often aren't necessary—the library
> will handle it for you.
> * Strings in SQL appear inside single-quotes (e.g. `'a string'`).
> Double-quotes mean something else.
> * Equality checks are done with `=`, not `==`.


### `CREATE TABLE`
`CREATE TABLE` statements, unsurprisingly, create new tables in the current
database. For example, the following command creates a table called "accounts".
Each account has a corresponding name, email, balance, and an age.
Each column in this table has an associated type: `name` and `email` are
strings (`TEXT`), `balance` is a float (`REAL`), and `age` is an integer
(`INTEGER`).

```sql
CREATE TABLE accounts (
    name TEXT,
    email TEXT,
    balance REAL,
    age INTEGER
);
```

Sometimes, you'll want to create a table only if it doesn't already exists. You
can use `CREATE TABLE IF NOT EXISTS` for that:

```sql
CREATE TABLE IF NOT EXISTS accounts (
    name TEXT,
    email TEXT,
    balance REAL,
    age INTEGER
);
```

Either way we do it, we've now create an empty `accounts` table in the database.

<p align="center">
<b>accounts</b>

| name    | email         | balance   | age |
| ------- | ------------- | --------- | --- |
</p>

### `INSERT`
You can use an `INSERT` statement to actually add rows to an existing table.

```sql
INSERT INTO accounts VALUES ('John Doe', 'john@doe.com', 250.45, 28);
```

We can also `INSERT` multiple rows at once:

```sql
INSERT INTO accounts VALUES
    ('Rose Parker', 'RoseCParker@teleworm.us', 12000, 31),
    ('Nathan Dandrea', 'NathanADandrea@dayrep.com', 50.9, 39),
    ('Flora Lewis', 'FloraKLewis@rhyta.com', 10.0, 25);
```

After all these insertions, our `accounts` table should look like this:

<p align="center">
<b>accounts</b>

| name                 | email                     | balance      | age |
| -------------------- | ------------------------- | ------------ | --- |
| John Doe             | john@doe.com              | 250.45       | 28  |
| Rose Parker          | RoseCParker@teleworm.us   | 12000        | 31  |
| Nathan Dandrea       | NathanADandrea@dayrep.com | 50.9         | 39  |
| Flora Lewis          | FloraKLewis@rhyta.comi    | 10           | 25  |
</p>

### SELECT
`SELECT` statements let you query existing tables to get the data they contain.
For example, you can get all the data in the `accounts` table by running:

```sql
SELECT * FROM accounts;
```

<p align="center">
<b>Query Result</b>

| name                 | email                     | balance      | age |
| -------------------- | ------------------------- | ------------ | --- |
| John Doe             | john@doe.com              | 250.45       | 28  |
| Rose Parker          | RoseCParker@teleworm.us   | 12000        | 31  |
| Nathan Dandrea       | NathanADandrea@dayrep.com | 50.9         | 39  |
| Flora Lewis          | FloraKLewis@rhyta.comi    | 10           | 25  |
</p>

In the query above, `*` means all columns. We can also refine our query by
only selecting certain columns:

```sql
SELECT name, age FROM accounts;
```

<p align="center">
<b>Query Result</b>

| name                 | age |
| -------------------- | --- |
| John Doe             | 28  |
| Rose Parker          | 31  |
| Nathan Dandrea       | 39  |
| Flora Lewis          | 25  |
</p>

We can use a `WHERE` clause to filter out some rows based on a condition:

```sql
SELECT * FROM accounts WHERE balance > 100;
```

<p align="center">
<b>Query Result</b>

| name                 | email                     | balance      | age |
| -------------------- | ------------------------- | ------------ | --- |
| John Doe             | john@doe.com              | 250.45       | 28  |
| Rose Parker          | RoseCParker@teleworm.us   | 12000        | 31  |
</p>

## Using SQLite with Python

To see how to run SQL commands like these from Python, please follow
[the official tutorial for Python's sqlite3 module](https://docs.python.org/3/library/sqlite3.html#tutorial).
Make sure to notice the usage of `con.commit()` after insertions.
Database engines typically work with transactions; if you update a table (e.g.
with `INSERT`), your changes will not actually make it to the database until
you finalize the transactions (with `con.commit()`).
