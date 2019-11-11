# CIA-factbook
SQL project using the Python package "pandas" and "sqlite3" to read and print queries from a .db file

# Project Details:
This project is a minor one that delves on reading a database and doing printing out various queries using pandas & sqlite3. In this project, we will be using the 2015 CIA Factbook, a collection of various statistics on different subjects for almost all of the countries on earth. Specifically, we will be using the demographic portion of the factbook. Here's a few of the categories within the "facts" table within factbook that we will use:

- population - The population as of 2015.
- population_growth - The annual population growth rate, as a percentage.
- area - The total land and water area.

# Reading the DB file and Preparing for Querying:
The first thing we need to do is to connect to the actual database file ("factbook.db") which we can do by using the .connect() function within the sqlite3 package.
```python
import sqlite3

conn = sqlite3.connect("factbook.db")
```
So we've connected to the db file but we don't know where to start querying. What we can do is print out the schema within the "sqllite_ master". The "sql_master" is a special table that defines the schema for the database. This exists in all db files being run via sql_lite. We can always run the below query to find all the tables within the schema:
```python
import pandas
# All queries should be within quotation marks
find_tables = "SELECT * FROM sqlite_master WHERE type='table';"

# Use read_sql_query() in the pandas package and send both the query and the db connection through the function.
print(pandas.read_sql_query(find_tables, conn))
```

|   |type |name |tbl_name |rootpage |sql |
|---|------|------|----------|----------|-----|
| 0 | table | facts | facts | 2 | CREATE TABLE "facts" ("id" INTEGER PRIMARY KEY... |
| 1 | table | sqlite_sequence | sqlite_sequence | 3 | CREATE TABLE sqlite_sequence(name,seq)|

So we found that within factbooks, there are two tables, "facts" and "sqlite_sequence". Let's print the first line in both tables and see which we should use for our other queries:

```python
facts_table = "SELECT * FROM facts LIMIT 1;"
print(pandas.read_sql_query(facts_table, conn))

sqlite_sequence = "SELECT * FROM sqlite_sequence LIMIT 1;"
print(pandas.read_sql_query(sqlite_sequence, conn))
```

| |id|code|name|area|area_land|area_water|population|population_growth|birth_rate|death_rate|migration_rate|created_at|updated_at|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|af|Afghanistan | 652230|652230|0|32564342|2.32|38.57|13.89|1.51|2015-11-01 13:19:49.461734|2015-11-01 13:19:49.461734|


| |name|seq|
|---|---|---|
|0 |facts|261|

From the looks of it, we should be using the "facts" table as it contains the actualy information or the countries.

