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

# Queries of the Facts Table
Alright, now that we know to use the Facts table, we can create and print some queries to show what you can do with sqlite3 & pandas:

1. Write a single query that returns the:
 - Minimum population
 - Maximum population
 - Minimum population growth
 - Maximum population growth
 
 ```python
 q1 = 'SELECT MIN(population), MAX(population), MIN(population_growth), MAX(population_growth)
       FROM facts;'
 
print(pandas.read_sql_query(q1, conn))
```
|   |MIN(population)  |MAX(population)  |MIN(population_growth)  |MAX(population_growth)|
|---|-----------------|-----------------|------------------------|----------------------|
|0  |             0   |      7256490011 |                    0.0 |                   4.02|


2. Write a query that returns the countrie(s) with the minimum population. Return all info on those countries:
```python
q2 = 'SELECT * 
      FROM facts 
      WHERE population == (SELECT MIN(population) 
                           FROM facts);'
                           
print(pandas.read_sql_query(q2, conn))
```
| |id|code|name|area|area_land|area_water|population|population_growth|birth_rate|death_rate|migration_rate|created_at|updated_at|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|250|ay|Antarctica|None|280000|None|0|None|None|None|None|2015-11-01 13:38:44.885746|2015-11-01 13:38:44.885746|


3 Write a query that returns the countrie(s) with the maximum population. Return all info on those countries:
```python
q3 = 'SELECT * 
      FROM facts
      WHERE population == (SELECT MAX(population)
                           FROM facts);'

print(pandas.read_sql_query(q3, conn))
```
| |id|code|name|area|area_land|area_water|population|population_growth|birth_rate|death_rate|migration_rate|created_at|updated_at|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|261|xx|World|None|None|None|7256490011|1.08|18.6|7.8|None|2015-11-01 13:39:09.910721|2015-11-01 13:39:09.910721|


4. List the top 10 countries that have the highest population density:
 - Population density is the ratio of population to land area
```python
q4 = 'SELECT name, CAST(population AS float)/CAST(area_land AS float) AS ratio
      FROM facts
      ORDER BY ratio DESC'

print(pandas.read_sql_query(q4, conn))
```
| |        name|         ratio|
|---|----------|--------------|
|0|       Macau|  21168.964286|
|1|     Monaco|  15267.500000|
|2|   Singapore|   8259.784571|
|3|   Hong Kong|   6655.271202|
|4|  Gaza Strip|   5191.819444|
|5|   Gibraltar|   4876.333333|
|6|     Bahrain|   1771.859211|
|7|    Maldives|   1319.640940|
|8|       Malta|   1310.015823|
|9|     Bermuda|   1299.925926|


5. Which countries have the highest ratios of water to land? Only list the countries that have more water than land:
```python
q5 = 'SELECT name, CAST(area_water AS float)/CAST(area_land AS float) AS ratio 
      FROM facts 
      WHERE area_water > area_land 
      ORDER BY ratio DESC'
      
print(pandas.read_sql_query(q5, conn))
```
| |                            name|       ratio|
|---|------------------------------|------------|
|0|  British Indian Ocean Territory|  905.666667|
|1|                  Virgin Islands|    4.520231|

