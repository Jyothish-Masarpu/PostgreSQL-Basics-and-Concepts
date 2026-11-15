# PostgreSQL Basics and Concepts

A collection of PostgreSQL concepts, commands, operators and built-in functions.

The purpose of this repository is to understand SQL concepts and learn when and how to use them.

---

# Table of Contents

1. [Introduction to SQL](#1-introduction-to-sql)
2. [Basic SQL Commands](#2-basic-sql-commands)
3. [SQL Query Execution Order](#3-sql-query-execution-order)
4. [SQL Operators](#4-sql-operators)
5. [Pattern Matching](#5-pattern-matching)
6. [Regular Expressions](#6-regular-expressions)
7. [String Functions](#7-string-functions)
8. [Type Conversion](#8-type-conversion)
9. [Aggregate Functions](#9-aggregate-functions)
10. [Grouping](#10-grouping)
11. [Joins](#11-joins)
12. [Subqueries](#12-subqueries)
13. [Conditional Expressions](#13-conditional-expressions)
14. [Set Operations](#14-set-operations)
15. [Date and Time Functions](#15-date-and-time-functions)
16. [Window Functions](#16-window-functions)
17. [Common Table Expressions](#17-common-table-expressions)
18. [NULL Values](#18-null-values)
19. [Useful SQL Tips](#19-useful-sql-tips)

---

# 1. Introduction to SQL

## What is SQL?

SQL stands for Structured Query Language.

It is used to communicate with relational databases.

We use SQL to:
- Retrieve data.
- Insert new records.
- Update existing records.
- Delete records.
- Create tables.
- Filter and sort data.
- Perform calculations.

## What is PostgreSQL?

PostgreSQL is an open-source relational database management system.

It supports SQL and provides additional features such as regular expressions, advanced data types, window functions and many built-in functions.

## Database, Schema and Table

- Database: Stores related information.
- Schema: Organizes database objects.
- Table: Stores data in rows and columns.
- Row: Represents one record.
- Column: Represents one type of information.

Example:

`cd.members`

Here:
- `cd` is the schema.
- `members` is the table.

---

# 2. Basic SQL Commands

## SELECT

Used to retrieve data from a table.

Syntax:

```sql
SELECT column_name
FROM table_name;
```

Example:

```sql
SELECT firstname
FROM cd.members;
```

To retrieve all columns:

```sql
SELECT *
FROM cd.members;
```

## FROM

Specifies the table from which data is retrieved.

```sql
SELECT *
FROM cd.members;
```

## WHERE

Used to filter rows based on a condition.

```sql
SELECT *
FROM cd.members
WHERE memid = 1;
```

Only records satisfying the condition are returned.

## DISTINCT

Removes duplicate values.

```sql
SELECT DISTINCT surname
FROM cd.members;
```

Each surname appears only once.

## ORDER BY

Sorts the result.

Ascending order:

```sql
SELECT *
FROM cd.members
ORDER BY surname ASC;
```

Descending order:

```sql
SELECT *
FROM cd.members
ORDER BY surname DESC;
```

`ASC` is the default sorting order.

## LIMIT

Restricts the number of returned rows.

```sql
SELECT *
FROM cd.members
LIMIT 5;
```

## OFFSET

Skips a specified number of rows.

```sql
SELECT *
FROM cd.members
LIMIT 5 OFFSET 2;
```

This skips the first two rows and returns the next five.

## AS (Alias)

Used to give a temporary name to a column or table.

```sql
SELECT firstname AS name
FROM cd.members;
```

The output column will be named `name`.

`AS` is optional for column aliases.

## SQL Comments

Single-line comment:

```sql
-- This is a comment
SELECT * FROM cd.members;
```

Multi-line comment:

```sql
/*
This is a multi-line comment.
*/
```

---

# 3. SQL Query Execution Order

We usually write SQL queries starting with SELECT, but PostgreSQL logically processes the main query clauses in a different order.

Consider:

```sql
SELECT surname, COUNT(*) AS total
FROM cd.members
WHERE recommendedby IS NOT NULL
GROUP BY surname
HAVING COUNT(*) > 1
ORDER BY total DESC
LIMIT 5;
```

Logical execution order:

| Order | Clause | Purpose |
|---|---|---|
| 1 | FROM | Identify source tables |
| 2 | WHERE | Filter individual rows |
| 3 | GROUP BY | Group similar rows |
| 4 | HAVING | Filter groups |
| 5 | SELECT | Select output columns |
| 6 | DISTINCT | Remove duplicates |
| 7 | ORDER BY | Sort results |
| 8 | LIMIT | Restrict returned rows |
| 9 | OFFSET | Skip rows when requested |

Important points:

- WHERE filters rows before grouping.
- HAVING filters groups after grouping.
- SELECT determines the output columns.
- ORDER BY sorts the final selected results.

This is the logical processing order. PostgreSQL may physically execute operations in a different order because of query optimization.

---

# 4. SQL Operators

## Comparison Operators

| Operator | Meaning |
|---|---|
| = | Equal |
| <> | Not equal |
| != | Not equal |
| > | Greater than |
| < | Less than |
| >= | Greater than or equal |
| <= | Less than or equal |

Example:

```sql
SELECT *
FROM cd.members
WHERE memid > 5;
```

## AND

Both conditions must be true.

```sql
SELECT *
FROM cd.members
WHERE memid > 2 AND surname = 'Smith';
```

## OR

At least one condition must be true.

```sql
SELECT *
FROM cd.members
WHERE surname = 'Smith' OR surname = 'Tracy';
```

## NOT

Reverses a condition.

```sql
SELECT *
FROM cd.members
WHERE NOT memid = 1;
```

## IN

Checks whether a value matches any value in a list.

```sql
SELECT *
FROM cd.members
WHERE memid IN (1, 2, 3);
```

## NOT IN

Excludes the specified values.

```sql
SELECT *
FROM cd.members
WHERE memid NOT IN (1, 2, 3);
```

## BETWEEN

Checks whether a value is within a range. Both boundaries are included.

```sql
SELECT *
FROM cd.members
WHERE memid BETWEEN 1 AND 5;
```

## IS NULL

Finds rows containing NULL values.

```sql
SELECT *
FROM cd.members
WHERE recommendedby IS NULL;
```

## IS NOT NULL

Finds rows that do not contain NULL.

```sql
SELECT *
FROM cd.members
WHERE recommendedby IS NOT NULL;
```

Important: Use `IS NULL` instead of `= NULL`.

---

# 5. Pattern Matching

Pattern matching is used to search for text based on a pattern.

PostgreSQL provides LIKE, ILIKE and SIMILAR TO.

## LIKE

Used for simple pattern matching.

Two important wildcards:

| Symbol | Meaning |
|---|---|
| % | Zero or more characters |
| _ | Exactly one character |

Example 1: Names starting with Tennis.

```sql
SELECT *
FROM cd.facilities
WHERE name LIKE 'Tennis%';
```

Example 2: Names ending with Court.

```sql
SELECT *
FROM cd.facilities
WHERE name LIKE '%Court';
```

Example 3: Names containing Tennis.

```sql
SELECT *
FROM cd.facilities
WHERE name LIKE '%Tennis%';
```

Example 4: Exactly one character before `ohn`.

```sql
SELECT *
FROM cd.members
WHERE firstname LIKE '_ohn';
```

LIKE is case-sensitive.

## ILIKE

ILIKE is PostgreSQL's case-insensitive version of LIKE.

Example:

```sql
SELECT *
FROM cd.members
WHERE firstname ILIKE 'jo%';
```

This can match:
- John
- john
- JOHN
- Jonathan

## SIMILAR TO

SIMILAR TO provides SQL-standard pattern matching with regular-expression-like features.

It matches the entire string.

Example 1: Names starting with Tennis.

```sql
SELECT *
FROM cd.facilities
WHERE name SIMILAR TO 'Tennis%';
```

Example 2: Names containing Tennis or Badminton.

```sql
SELECT *
FROM cd.facilities
WHERE name SIMILAR TO '%(Tennis|Badminton)%';
```

Example 3: Names starting with A, B or C.

```sql
SELECT *
FROM cd.members
WHERE firstname SIMILAR TO '[ABC]%';
```

Some important symbols:

| Symbol | Meaning |
|---|---|
| % | Zero or more characters |
| _ | Exactly one character |
| [] | Character set |
| \| | OR |
| * | Zero or more repetitions |
| + | One or more repetitions |

---

# 6. Regular Expressions

Regular expressions are used to search for more complex text patterns.

PostgreSQL supports POSIX regular expressions using the `~` operator.

Unlike LIKE and SIMILAR TO, `~` can match a pattern anywhere inside a string without requiring the entire string to match.

## Important Regular Expression Symbols

| Symbol | Meaning |
|---|---|
| ^ | Beginning of string |
| $ | End of string |
| . | Any single character |
| * | Zero or more repetitions |
| + | One or more repetitions |
| ? | Zero or one occurrence |
| [] | Character set |
| [0-9] | Any digit |
| [A-Z] | Uppercase letters |
| [a-z] | Lowercase letters |
| [^abc] | Any character except a, b or c |
| \| | OR |
| () | Groups expressions |

## Example 1: Find names starting with Tennis.

```sql
SELECT *
FROM cd.facilities
WHERE name ~ '^Tennis';
```

## Example 2: Find names ending with Court.

```sql
SELECT *
FROM cd.facilities
WHERE name ~ 'Court$';
```

## Example 3: Find telephone numbers containing parentheses.

```sql
SELECT *
FROM cd.members
WHERE telephone ~ '[()]';
```

## Example 4: Find names containing digits.

```sql
SELECT *
FROM cd.members
WHERE firstname ~ '[0-9]';
```

## Regular Expression Operators

| Operator | Meaning |
|---|---|
| ~ | Case-sensitive matching |
| ~* | Case-insensitive matching |
| !~ | Does not match |
| !~* | Does not match, ignoring case |

Example:

```sql
SELECT *
FROM cd.members
WHERE firstname ~* '^john';
```

This finds names starting with John, regardless of uppercase or lowercase.

## LIKE vs SIMILAR TO vs ~

| Feature | LIKE | SIMILAR TO | ~ |
|---|---|---|---|
| Pattern matching | Yes | Yes | Yes |
| % wildcard | Yes | Yes | No |
| _ wildcard | Yes | Yes | No |
| ^ beginning | No | No | Yes |
| $ ending | No | No | Yes |
| Character sets | No | Yes | Yes |
| Regular expressions | No | Limited SQL-style syntax | POSIX regex |
| Case-insensitive option | ILIKE | No direct ILIKE equivalent | ~* |
| Full-string matching | Yes | Yes | No |

When to use:

- LIKE: Simple searches.
- ILIKE: Simple case-insensitive searches.
- SIMILAR TO: SQL-standard pattern matching.
- ~: Advanced regular expression searches.

---

# 7. String Functions

String functions are used to manipulate and format text.

## UPPER()

Converts text into uppercase.

```sql
SELECT UPPER('hello');
```

Output:

```text
HELLO
```

## LOWER()

Converts text into lowercase.

```sql
SELECT LOWER('HELLO');
```

Output:

```text
hello
```

## LENGTH()

Returns the number of characters in a string.

```sql
SELECT LENGTH('PostgreSQL');
```

Output:

```text
10
```

## TRIM()

Removes spaces from the beginning and end of a string.

```sql
SELECT TRIM('  Jyothish  ');
```

Output:

```text
Jyothish
```

## LTRIM()

Removes characters from the left side.

```sql
SELECT LTRIM('   SQL');
```

Output:

```text
SQL
```

## RTRIM()

Removes characters from the right side.

```sql
SELECT RTRIM('SQL   ');
```

Output:

```text
SQL
```

## REPLACE()

Replaces specific text with another value.

Syntax:

```sql
REPLACE(string, old_string, new_string)
```

Example:

```sql
SELECT REPLACE('555-555-5555', '-', '');
```

Output:

```text
5555555555
```

To remove spaces:

```sql
SELECT REPLACE('Hello World', ' ', '');
```

Output:

```text
HelloWorld
```

An empty string `''` means replace with nothing.

## REGEXP_REPLACE()

Replaces text using a regular expression pattern.

Syntax:

```sql
REGEXP_REPLACE(string, pattern, replacement, flags)
```

Example:

```sql
SELECT REGEXP_REPLACE('abc123def456', '[0-9]', '', 'g');
```

Output:

```text
abcdef
```

The `g` flag means global replacement. It replaces all matching occurrences.

Without `g`, only the first matching occurrence is replaced.

## SUBSTRING()

Extracts a part of a string.

Syntax:

```sql
SUBSTRING(string FROM start FOR length)
```

Example:

```sql
SELECT SUBSTRING('Jyothish' FROM 1 FOR 3);
```

Output:

```text
Jyo
```

Another syntax:

```sql
SELECT SUBSTRING('Jyothish', 1, 3);
```

## CONCAT()

Joins multiple strings together.

```sql
SELECT CONCAT('Hello', ' ', 'World');
```

Output:

```text
Hello World
```

## String Concatenation (||)

The `||` operator joins strings.

```sql
SELECT 'Hello' || ' ' || 'World';
```

Output:

```text
Hello World
```

Example with table columns:

```sql
SELECT surname || ', ' || firstname AS name
FROM cd.members;
```

## LPAD()

Adds characters to the left side of a string until it reaches the required length.

Syntax:

```sql
LPAD(string, length, fill)
```

Example:

```sql
SELECT LPAD('123', 5, '0');
```

Output:

```text
00123
```

Another example:

```sql
SELECT LPAD('SQL', 6, '*');
```

Output:

```text
***SQL
```

If the original string is longer than the requested length, PostgreSQL truncates it from the right.

## RPAD()

Adds characters to the right side.

```sql
SELECT RPAD('SQL', 6, '*');
```

Output:

```text
SQL***
```

## POSITION()

Finds the position of a substring inside another string.

```sql
SELECT POSITION('SQL' IN 'Learn SQL');
```

Output:

```text
7
```

Positions start from 1. If the substring is not found, the result is 0.

## TRANSLATE()

Replaces individual characters using a character-to-character mapping.

Syntax:

```sql
TRANSLATE(string, characters_to_replace, replacement_characters)
```

Example:

```sql
SELECT TRANSLATE('123-456-7890', '-', '');
```

Output:

```text
1234567890
```

It is especially useful when removing multiple individual characters.

Example:

```sql
SELECT TRANSLATE('(844) 693-0723', '-() ', '');
```

Output:

```text
8446930723
```

---

# 8. Type Conversion

Type conversion means changing a value from one data type to another.

## CAST()

Converts a value into another data type.

Syntax:

```sql
CAST(value AS data_type)
```

Example:

```sql
SELECT CAST(123 AS TEXT);
```

Output:

```text
123
```

Convert text into an integer:

```sql
SELECT CAST('100' AS INTEGER);
```

Output:

```text
100
```

Example with ZIP codes:

```sql
SELECT LPAD(CAST(zipcode AS CHAR(5)), 5, '0')
FROM cd.members;
```

Here:
1. CAST converts the ZIP code into a character value.
2. LPAD adds zeros to the left.
3. The final result has a length of five characters.

## :: Operator

PostgreSQL also supports a shorter way to convert data types.

```sql
SELECT 123::TEXT;
```

This is equivalent to:

```sql
SELECT CAST(123 AS TEXT);
```

## Common Data Types

| Data type | Purpose |
|---|---|
| INTEGER | Whole numbers |
| BIGINT | Large whole numbers |
| NUMERIC | Exact decimal numbers |
| REAL | Floating-point numbers |
| CHAR(n) | Fixed-length character data |
| VARCHAR(n) | Variable-length character data |
| TEXT | Variable-length text |
| BOOLEAN | TRUE or FALSE |
| DATE | Date values |
| TIME | Time values |
| TIMESTAMP | Date and time |

---

# 9. Aggregate Functions

Aggregate functions perform calculations on multiple rows and return a single result per group.

## COUNT()

Counts rows.

```sql
SELECT COUNT(*)
FROM cd.members;
```

Counts all rows.

To count non-NULL values in a particular column:

```sql
SELECT COUNT(recommendedby)
FROM cd.members;
```

## SUM()

Calculates the total.

```sql
SELECT SUM(guestcost)
FROM cd.facilities;
```

## AVG()

Calculates the average.

```sql
SELECT AVG(membercost)
FROM cd.facilities;
```

## MIN()

Returns the smallest value.

```sql
SELECT MIN(membercost)
FROM cd.facilities;
```

## MAX()

Returns the largest value.

```sql
SELECT MAX(membercost)
FROM cd.facilities;
```

### Important Difference

| Function | Purpose |
|---|---|
| COUNT(*) | Counts all rows |
| COUNT(column) | Counts non-NULL values |
| SUM() | Calculates total |
| AVG() | Calculates average |
| MIN() | Finds minimum |
| MAX() | Finds maximum |

---

# 10. Grouping

## GROUP BY

Groups rows that have the same values.

Example:

```sql
SELECT recommendedby, COUNT(*)
FROM cd.members
GROUP BY recommendedby;
```

This counts how many members were recommended by each member.

## HAVING

Filters groups after GROUP BY.

```sql
SELECT recommendedby, COUNT(*)
FROM cd.members
GROUP BY recommendedby
HAVING COUNT(*) > 1;
```

Only groups with more than one member are returned.

## WHERE vs HAVING

| WHERE | HAVING |
|---|---|
| Filters individual rows | Filters groups |
| Used before GROUP BY | Used after GROUP BY |
| Cannot directly use aggregate conditions | Can use aggregate conditions |

---

# 11. Joins

Joins combine data from multiple tables using related columns.

## INNER JOIN

Returns only matching rows from both tables.

```sql
SELECT m.firstname, b.starttime
FROM cd.members m
INNER JOIN cd.bookings b
ON m.memid = b.memid;
```

## LEFT JOIN

Returns all rows from the left table and matching rows from the right table.

If no match exists, right-side columns contain NULL.

```sql
SELECT m.firstname, b.starttime
FROM cd.members m
LEFT JOIN cd.bookings b
ON m.memid = b.memid;
```

## RIGHT JOIN

Returns all rows from the right table and matching rows from the left table.

```sql
SELECT m.firstname, b.starttime
FROM cd.members m
RIGHT JOIN cd.bookings b
ON m.memid = b.memid;
```

## FULL OUTER JOIN

Returns matching and non-matching rows from both tables.

```sql
SELECT m.firstname, b.starttime
FROM cd.members m
FULL OUTER JOIN cd.bookings b
ON m.memid = b.memid;
```

## CROSS JOIN

Returns every possible combination of rows from both tables.

```sql
SELECT *
FROM cd.members
CROSS JOIN cd.facilities;
```

## SELF JOIN

Joins a table with itself.

Example: Find members and the members who recommended them.

```sql
SELECT
    m.firstname AS member,
    r.firstname AS recommender
FROM cd.members m
LEFT JOIN cd.members r
ON m.recommendedby = r.memid;
```

---

# 12. Subqueries

A subquery is a query written inside another query.

Example:

```sql
SELECT *
FROM cd.facilities
WHERE membercost > (
    SELECT AVG(membercost)
    FROM cd.facilities
);
```

The inner query calculates the average member cost.

The outer query finds facilities whose member cost is greater than that average.

## IN with Subquery

```sql
SELECT *
FROM cd.members
WHERE memid IN (
    SELECT memid
    FROM cd.bookings
);
```

Finds members who have bookings.

## EXISTS

Checks whether a subquery returns at least one row.

```sql
SELECT *
FROM cd.members m
WHERE EXISTS (
    SELECT 1
    FROM cd.bookings b
    WHERE b.memid = m.memid
);
```

## NOT EXISTS

Checks whether a subquery returns no rows.

```sql
SELECT *
FROM cd.members m
WHERE NOT EXISTS (
    SELECT 1
    FROM cd.bookings b
    WHERE b.memid = m.memid
);
```

Finds members who have no bookings.

---

# 13. Conditional Expressions

## CASE WHEN

Used to apply conditions and return different results.

Syntax:

```sql
CASE
    WHEN condition THEN result
    WHEN condition THEN result
    ELSE result
END
```

Example:

```sql
SELECT name,
       CASE
           WHEN membercost = 0 THEN 'Free'
           ELSE 'Paid'
       END AS membership_type
FROM cd.facilities;
```

If membercost is zero, the result is Free. Otherwise, it is Paid.

---

# 14. Set Operations

Set operations combine the results of multiple SELECT queries.

## UNION

Combines results and removes duplicates.

```sql
SELECT firstname FROM cd.members
UNION
SELECT name FROM cd.facilities;
```

Both queries must return the same number of columns with compatible data types.

## UNION ALL

Combines results without removing duplicates.

```sql
SELECT firstname FROM cd.members
UNION ALL
SELECT name FROM cd.facilities;
```

## INTERSECT

Returns rows present in both query results.

```sql
SELECT memid FROM cd.members
INTERSECT
SELECT memid FROM cd.bookings;
```

## EXCEPT

Returns rows from the first query that are not present in the second.

```sql
SELECT memid FROM cd.members
EXCEPT
SELECT memid FROM cd.bookings;
```

---

# 15. Date and Time Functions

## CURRENT_DATE

Returns the current date.

```sql
SELECT CURRENT_DATE;
```

## NOW()

Returns the current date and time.

```sql
SELECT NOW();
```

## EXTRACT()

Extracts a particular part of a date or timestamp.

```sql
SELECT EXTRACT(YEAR FROM TIMESTAMP '2026-11-14 10:30:00');
```

Output:

```text
2026
```

Other examples:

```sql
SELECT EXTRACT(MONTH FROM CURRENT_DATE);
```

```sql
SELECT EXTRACT(DAY FROM CURRENT_DATE);
```

## DATE_TRUNC()

Truncates a date or timestamp to a specified precision.

```sql
SELECT DATE_TRUNC('month', TIMESTAMP '2026-11-14 10:30:00');
```

Output:

```text
2026-11-01 00:00:00
```

## INTERVAL

Used to represent a period of time.

```sql
SELECT CURRENT_DATE + INTERVAL '7 days';
```

Other examples:

```sql
SELECT CURRENT_DATE + INTERVAL '2 months';
```

```sql
SELECT CURRENT_DATE - INTERVAL '1 year';
```

---

# 16. Window Functions

Window functions perform calculations across related rows without combining them into one row.

## OVER()

Defines the window of rows used by a window function.

```sql
SELECT
    memid,
    firstname,
    COUNT(*) OVER() AS total_members
FROM cd.members;
```

Every row displays the total number of members.

## ROW_NUMBER()

Assigns a unique sequential number to each row.

```sql
SELECT
    memid,
    firstname,
    ROW_NUMBER() OVER(ORDER BY memid) AS row_num
FROM cd.members;
```

## RANK()

Assigns rankings. Equal values receive the same rank, and the next rank is skipped.

```sql
SELECT
    name,
    membercost,
    RANK() OVER(ORDER BY membercost DESC) AS ranking
FROM cd.facilities;
```

## DENSE_RANK()

Assigns rankings without skipping numbers after ties.

```sql
SELECT
    name,
    membercost,
    DENSE_RANK() OVER(ORDER BY membercost DESC) AS ranking
FROM cd.facilities;
```

## PARTITION BY

Divides rows into groups for window calculations.

```sql
SELECT
    memid,
    facid,
    COUNT(*) OVER(PARTITION BY facid) AS total_bookings
FROM cd.bookings;
```

It calculates the booking count separately for each facility.

---

# 17. Common Table Expressions

CTEs make complex queries easier to read.

They are created using the WITH keyword.

Example:

```sql
WITH facility_average AS (
    SELECT AVG(membercost) AS average_cost
    FROM cd.facilities
)
SELECT *
FROM cd.facilities
WHERE membercost > (
    SELECT average_cost
    FROM facility_average
);
```

The CTE calculates the average cost first.

The main query uses that result.

---

# 18. NULL Values

NULL means a value is missing or unknown.

It is not the same as:
- Zero.
- Empty string.
- False.

Example:

```sql
SELECT *
FROM cd.members
WHERE recommendedby IS NULL;
```

Finds members who have no recommender.

To replace NULL with another value, use COALESCE().

```sql
SELECT COALESCE(NULL, 'Unknown');
```

Output:

```text
Unknown
```

Example with a table:

```sql
SELECT
    firstname,
    COALESCE(recommendedby, 0) AS recommendedby
FROM cd.members;
```

---

# 19. Useful SQL Tips

1. Use SELECT * when exploring a table, but select only required columns in final queries.
2. Use meaningful column aliases.
3. Use WHERE for row filtering.
4. Use HAVING for group filtering.
5. Use GROUP BY when aggregate calculations are needed for each category.
6. Use JOIN when information is available in multiple related tables.
7. Use subqueries when one query depends on another query's result.
8. Use LIKE for simple pattern matching.
9. Use regular expressions for complex text searches.
10. Use CAST when a value needs a different data type.
11. Use EXPLAIN to understand how PostgreSQL plans to execute a query.
12. Always test queries before using them in real applications.

---

## Learning Reference

This repository is a personal reference for learning PostgreSQL.

For practical SQL exercises and their solutions, visit:

[PostgreSQL Exercises Solutions](https://github.com/Jyothish-Masarpu/PostgreSQL-Exercises-Solutions)

For official PostgreSQL documentation:

https://www.postgresql.org/docs/
