# rogue-sql 

Preprocesses enhanced pseudo-SQL into legal SQL

_Queries are built on hope._

## This is vaporware!

This project is at the first stage of README-driven 
development.  I'm imagining desired features.

## Purpose 

Rogue SQL imagines the beautiful, powerful, 
easy-to-use SQL that could exist, and permits you 
to write *that*, translating it into boring restrictive 
SQL that your database will actually accept.

Most features require a working database connection 
to get needed metadata, but a few can operate without.

## Install

`pip install rogue_sql` 

## Using

### Through Python 

    from rogue_sql import legalize

    db_conn_str = "postgresql://will:longliveliz@localhost/shakes"
    awesome_sql = "SELECT -c, -e, FROM table_with_a_b_c_d_e"
    legalize(awesome_sql, db_conn_str)

    "SELECT a, b, d FROM table_with_a_b_c_d_e"

### From command-line 

    $ rogue_sql "SELECT -c, -e FROM tbl" "postgresql://will:longliveliz@localhost/shakes"

### Within [dbcli](https://www.dbcli.com/)

### Within [ipython-sql](https://github.com/catherinedevlin/ipython-sql)

## Features

Suppose `tbl` has string columns `s1` and `s2`, date column `d1` and `d2`, and integer columns `i1` and `i2`.

Most features require a working database connection.
The exceptions are marked with an asterisk.

### Column omissions

    SELECT -s1, -d2 FROM tbl 

to

    SELECT s2, d1, i1, i2 FROM tbl 

### Filter on multiple columns 

    SELECT * FROM tbl 
    WHERE * = 100

to

    SELECT * FROM tbl 
    WHERE i1 = 100
    OR    i2 = 100

### Permissive and composable clauses*

    SELECT * FROM tbl 
    WHERE i2 > 10
    ORDER BY d1 
    WHERE s1 like '%Python%'

to

    SELECT * FROM tbl 
    WHERE i2 > 10
    AND   s1 like '%Python%'
    ORDER BY d1 

### Add implied GROUP BY*

    SELECT s1, i1, max(d1)
    FROM   tbl 

to 

    SELECT s1, i1, max(d1)
    FROM   tbl 
    GROUP BY s1, i1

### Tolerate trailing commas*

    SELECT s1, s2, FROM tbl

to

    SELECT s1, s2 FROM tbl

### Reorder select columns 

    raw = "SELECT i2, s1, d2, d1, s2 FROM tbl"

`legalize(raw, reorder='alpha')`

    "SELECT d1, d2, i2, s1, s2 FROM tbl"

Or match the order in table definition: 

`legalize(raw, reorder='definition')`

    "SELECT s1, s2, d1, d2, i2 FROM tbl"

\* - usable without a database connection
