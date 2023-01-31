## 52-52 - Query Along
Although we provide exercises throughout the sections, we also recommend that you follow along by also writing out what you see
on the screen and trying the SQL commands yourself that we show you. The best way to learn is not by watching us do it, but by
practicing the commands yourself. So Query Along!

## 53-53 - Starting With Query
![](../img/53-53-1.png)

Select statement is to retrieve data.

## 54-54 - Exercise Simple Queries
When you're in the schema overview or the schema browser, you'll see all of the DBs.

In the DB, you have a schema called public.

**Today means 9999-01-01**.

## 55-55 - Changing Column Names in a SELECT Query
```sql
SELECT column AS "<new name>"
```

Double quotes are for column names.

## 56-56 - Concat Function

Column concatenation:

Single quotes are for characters.

```sql
SELECT concat(emp_no, ' is a ', title) AS "Employee Title" FROM titles;

SELECT emp_no, concat(first_name, ' ', last_name) AS "full name" FROM employees;
```

When doing concat(), the column name would become concat(), so rename that column.

You can concat text to text and column to column. Text is done in single quotes whereas renaming columns is done with double quotes.

## 57-57 - What Is A Function In SQL
We have 2 types of functions:
1) aggregate: They take data and produce one single value. Aggregate functions operate on many records to produce 1 value. Like summing all the salaries.
An aggregate function produces a summary.
2) scalar(non-aggregate): They run against each individual row. So you give them inputs and they're gonna run against each individual row, so you get 
multiple outputs. These functions operate on each record independently. Like concat() function.

## 58-58 - Aggregate Functions
![](../img/58-58-1.png)

```sql
SELECT count(emp_no) FROM employees; -- count() is an aggregate function
```

## 59-59 - Exercise Aggregate Functions
File
## 60-60 - Commenting Your Queries
In valentina DB, when writing SQL, you can double click a table to automatically write the name of the table!

https://www.red-gate.com/simple-talk/sql/oracle/how-to-make-comments-the-most-important-code-you-write/

## 61-61 - Common SELECT Mistakes
- Using " instead of ': Single quotes are the way that you write text in SQL, do not use double quotes for this. Double quotes are for tables. So:
**" is for tables and ' is for text**. SQL is case-insensitive. You can name the table users, Users, useRs ... . Now depending on how you type it,
sql may not be able to interpret it.
- case sensitive tables: Double quotes can tell you what type of table you're operating on. If the name of the table is all lower case, you can
just write it without double quotes, if one or more than one character of the table is uppercase or if there are some spaces inside the name of
the table, you definitely need to use double quotes for the name of the table. So always make sure that you keep your naming conventions consistent, so that
you can decide if you need double quotes or if you don't need double quotes
ex:
```sql
SELECT fullName as "full name" FROM users;
SELECT fullName as "full name" FROM "Users";
SELECT fullName as "full name" FROM "USERS";
SELECT fullName as "full name" FROM users;
```

## 62-62 - Filtering Data
All female employees:
```sql
SELECT * FROM employees WHERE gender = 'F';
```

## 63-63 - AND and OR
The order of operations: 

When SQL sees an AND, it's gonna chain that AND to the previous statement, and when it sees an OR, it's gonna start checking a completely different thing.
So when we have: a AND b AND c OR d AND e => (a AND b AND c) OR (d AND e).

## 64-64 - Exercise Filtering Data
How many female customers do we have from the state of oregon and new york?
```sql
SELECT count(firstname) FROM customers WHERE (state = 'OR' OR state = 'NY') AND gender = 'F';
```

Parentheses are important when we're doing filters.

## 65-65 - The NOT Keyword
```sql
SELECT COUNT(age) FROM customers WHERE NOT age = 55 AND NOT age = 20;
```
## 66-66 - Exercise The Where Clause
File

## 67-67 - Comparison Operators
Comparison operators are often most used for number comparisons.

In text, we would have: `'abc' > 'ace'` (b is ahead of c in alphabet)

Who over the age of 44 has an income of 100000?


## 68-68 - Exercise Comparison Operators
File
## 69-69 - Logical Operators

## 70-70 - Operator Precedence
https://www.postgresql.org/docs/12/sql-expressions.html#SYNTAX-EXPRESS-EVAL

## 71-71 - Operator Precedence 2
Priority: A statement having multiple operators is evaluated based on the priority of operators.

![](../img/71-71-1.png)

In this statement:
```sql
SELECT state, gender FROM customers WHERE gender = 'F' AND state = 'OR' OR gender = 'F' AND state = 'NY';
```
Mental model of above query:

We have 2 filters going on(this is just a mental model and DBMS may optimize it in different ways):
1. gender female 1. gender female
2. from Oregon 2. from NY

```sql
SELECT state, gender FROM customers WHERE gender = 'F' AND (state = 'OR' OR state = 'NY');
```
Mental model:
1. FROM Oregon
2. FROM NY
3. gender F

Because of perianthes, we don't look at it as two separate filters.

Ex: What is the precedence?
`
age > 20
AND salary > 1000
AND gender = 'f'
AND NOT state = 'NY'
`

A:

1. not from NY
2. older than 20
3. salary > 1000
4. gender female

Ex: What is the precedence?
`
(age > 20 OR age < 30)
AND salary > 1000
AND NOT state = 'NY'
AND NOT state = 'OR'
`

1. ages between 21 AND 29
2. not from NY
3. not from OR
4. salary > 1000

Ex: Precedence?
`
age > 20
OR age < 30
AND salary > 1000
AND NOT state = 'NY'
AND NOT state = 'OR'
`

A:

We're gonna have 2 filters going on:

Filter 1                        Filter 2
1. younger than 30              1. older than 20
2. not from NY
3. not from OR
4. salary > 1000

There are 2 distinct filters going on. We have 2 filters and they're gonna merge and you're gonna get results back from both.

Because we removed the parentheses, we can now view that OR statement as a clear separator between what's gonna happen before OR and after OR
and because the OR operator has the least importance, it's gonna fired last. 

### Direction

ex:
`
age > 20
OR age < 30
OR salary > 1000
`

We have 3 distinct filters going on:

Filter 1                    Filter 2                Filter 3
1. younger than 30          1. order than 20        1. salary > 1000

### Priority and direction
But when we have:
```sql
(
    salary > 10000 AND state = 'NY'
        OR (
        (age > 20 AND age < 30) AND (salary <= 20000)
    )
)
AND gender = 'F'
```
There are 2 filters going on:

Filter 1                    Filter 2
1. salary > 10000           1. between 21 and 29
2. from NY                  2. salary lower than or equal to 20000
3. female                   3. female

## 72-72 - Operator Precedence Extra
File

In the previous videos you learned about operator precedence an extremely important and vital concept to applying filters to your query. 
It is also an extremely complex chain of operations to follow at times.

Always remember the following: HIGHEST to LOWEST

1. Parenthesis

2. Arithmetic Operators

3. Concatenation Operators

4. Comparison Conditions

5. IS NULL, LIKE, NOT IN, etc.

6. NOT

7. AND

8. OR

Note here that OR is the lowest on the totem pole, we also need to make a couple of corrections, in the videos you saw the following queries:

age > 20
AND salary > 1000
AND gender = 'f'
AND NOT state = 'NY'

The order here is:

1. Age > 20

2. Salary > 1000

3. NOT from NY

4. Gender female

You also saw:

age > 20

OR age < 30

AND salary > 1000

AND NOT state = 'NY'

AND NOT state = 'OR'


The order here is:

1. Over 20

2. Younger than 30

3. Salary > 1000

4. NOT from NY

5. NOT from OR



If we did the following:

age > 20

OR age < 30

AND salary > 1000

AND (NOT state = 'NY'

AND NOT state = 'OR')

The order here is (due to parenthesis):

1. NOT from NY

2. NOT from OR

3. Over 20

4. Younger than 30

5. Salary > 1000

## 73-73 - Exercise Operator Precedence
File
TODO

## 74-74 - Checking For NULL Values
Should I use null?

Be careful

Be mindful

Be deliberate

By default every field is nullable.

Tip: Null is a special value:
`SELECT null = null` will output `null` and `SELECT null <> null` will output `null`!

No matter what you do with null, it will always be null. Subtract, divide, equal, ... . So if you try to do sth with a null value,
you're gonna get back null!

## 75-75 - IS Keyword
When we should make a field nullable?

- Is my data optional or required?
- future info?
- rational?

Anything you do with NULL, returns NULL:
```sql
SELECT name FROM "Student" WHERE name != NULL; -- wrong - we can't do operations with NULL.
```
This would result in nothing being returned. Because we're doing some operation with null. So how do we do this? We can use the `is` operator.

**The `is` operator allows you to filter out values that are null, not null, true or false.**
`
SELECT * FROM <table> WHERE <field> IS [NOT] NULL;
`

`
SELECT * FROM <table> WHERE <field> = '' IS NOT FALSE; -- same as saying: IS TRUE
`

Everyone that is not 20:
```sql
SELECT * FROM users WHERE age = 20 IS FALSE;

-- this reads better:
SELECT * FROM users WHERE age != 20;
```

With IS keyword we can identify NULL values.

## 76-76 - NULL Coalescing
Cleaning up your data: Replacing those NULL values with sane values. How would we do that?

We would do null value substitution. It is the ability to replace NULL values to operate on the data.

There is a function called coalesce which allows us to do this:

```sql
SELECT coalesce(<column>, 'Empty') AS column_alias FROM <table>; -- 'Empty' is the fallback value
```

Since we're running a function against a column, we have to alias the column name if we don't want it to be named after the function that
was executed upon it. Because when you execute a function on a column, it loses it's' column name, because you're now sensibly changing that data.

Null value substitution:

Coalesce returns the last non-null value in a list.

We can give coalesce, multiple columns to fall back on. So, if you had a table that had multiple columns that may or may not contain 
data, you could coalesce against each column:
```sql
SELECT coalesce(<column 1>, <column 2>, <column 3>, 'Empty') As combined_values FROM <table>;
```
In above example, column 1 is the first one that it's gonna check. If it doesn't have a null value, then that's the one we're returning. But if it has a null
value, we're gonna fall back(fall through) on column2, If column2 doesn't have a null value, we return that value, but if it has a null value again,
we fall through column3. If column3 has null value, return 'Empty', else return it's value

```sql
SELECT sum(coalesce(age, 20)) FROM "student";
```

## 77-77 - Exercise Null Value Coalescing
File
## 78-78 - 3 Valued Logic
Besides true and false, the result of logical expressions can also be unknown.

In most programming languages, unknown would often result in false. It makes sense, if you don;t know sth, it must be false.
But in SQL, there's a slightly different approach and we see that earlier because:
```sql
SELECT null = null -- returns null not true
```

![](../img/78-78-1.png)

Depending on what we do, a value could be null and if it's null and we compare against it in some logical fashion, t may return us null,
it may return us unknown. 

So 3-valued logic means each expression can have 3 different outcomes: true, false, null. We want to avoid null as much as possible(not always though!),

Null doesn't mean it's' nothing, it just means we don't know what it is. The SQL null value could be anything. SQL doesn't know about it, itt could be anything.

Nothing equals null, not even null itself. Each null could be different. 

Null equals null is not true, not false, it's null(SQL doesn't know this). 

That is why we use the IS NULL check. Because we can''t null to other things, we need a special operator in order to tell us if sth null.

EX)

`(NULL = 1) OR (1 = 1)`

Note: NULL = 1 returns NULL

The output is: true. Because when we have an OR, not actually but conceptually, we're starting a separate filter. So: NULL OR true. Since one of the sides of
OR is true, it returns true.

EX)

(NULL = 1) AND (0 = 1)

returns null. Because null AND false is null.

NULL is always unknown.

ex)

`
SELECT <column> FROM <table> WHERE <column> = NULL
`

is gonna return null. We're not gonna get any result.

Instead do:

`
SELECT <column> FROM <table> WHERE <column> IS NULL
`

### Strange scenarios:

ex)

```sql
SELECT <column> FROM <table> WHERE (<column> = NULL) OR (NOT <column> = NULL)
```

We know:

- sth = NULL -> returns null
- NOT sth = NULL -> also returns null because NOT NULL returns NULL
- NULL OR NULL -> NULL

So nothing will be returned.

`SELECT <column> FROM <table> WHERE (<column> IS NULL) OR (<column IS NOT NULL>)`

Returns everything(the entire table)!

**Note:** The equal sign with NULL doesn't work. Instead use the `IS` keyword.

SQL differs from many programming languages because that it has 3 valued logic.

## 79-79 - Exercise 3 Valued Logic
File
## 80-80 - BETWEEN AND
Shorthand to match against a range of values.

```sql
SELECT <column> FROM <table> WHERE <column> BETWEEN X AND Y
```
We're doing an inclusive range search. Anything that is X oor Y or in between, will be returned and this counts for dates, numbers and ... .

The equivalent is:
```sql
SELECT <column> FROM <table> WHERE <column> >= X AND <column> <= Y
```

Note that BETWEEN AND is sensitive to the order of arguments.

We use it because it's more readable and maintainable.

## 81-81 - Exercise BETWEEN AND
File
## 82-82 - IN Keyword
What if I want to find multiple values but not write endless AND statements?

The IN keyword is used to check if a value matches any value in a list of values.

```sql
SELECT * FROM <table> WHERE <column> IN (value1, value2, ...)

-- equivalent:
SELECT * FROM <table> WHERE <column> = <value1> OR <column> = <value2> OR ...
```

## 83-83 - Exercise IN Keyword
File
## 84-84 - LIKE
### Partial lookups:
What if you don't know exactly what you're searching for?

```sql
SELECT first_name FROM employees WHERE first_name LIKE 'M%'; -- get everyone who's name start with 'M'
```

Pattern matching:

In order to use LIKE you need to build patterns to match! What kind of patterns can we match?

Pattern matching happens with wildcards. Wildcard is placeholder that we can put in a for example LIKE operator to match parts of strings.

| Patten wildcard |   meaning  |
|-----------------|-----|
| %               |  any number of characters   |
| _               |   1 character  |


![](../img/83-83-1.png)

### Like keyword
Postgres LIKE **only** does text comparison, so we must **cast** whatever we use to text.

Postgres only does the LIKe operator against text.

There are 2 different ways of casting:

### Casting to text

```sql
CAST(salary AS text); -- salary is a number column

-- shorthand version
salary::text
```

### ILIKE keyword
case insensitive matching

```sql
name ILIKE '%MO';
```

## 85-85 - Exercise Like Keyword


## 86-86 - Dates And Timezones
GMT = Greenwich mean time

UTC = universal time coordinate

UTC is a standard. It's a way to measure time, it's not a timezone, no country in the world is using UTC to determine it's time.

What's the difference?

GMT is a timezone and UTC is a time standard.

For an app that everyone in the world could use, we have to use UTC. Why we can't just GMT?

UTC and GMT share the same current time. In essence they are the same, but conceptually they're different.

Why we can't use our local time zone and just offset everything?

We could! But it doesn't scale.

ALWAYS USE UTC:

```sql
SET TIME ZONE 'UTC'; -- sets the current session in UTC
SHOW TIMEZONE;
```

No territories use UTC

**Postgres saves everything already in UTC.** They go even further, they assume everything you give it that is date or time, is UTC. We have to
do special operations in order to table postgres that this is of a different timezone.

## 87-87 - Setting Up Timezones
How set the timezone for all of the sessions of the user you're logged in with?

Note: On windows or linux, your user should be `postgres`.

But if you're on mac and you have postgres.app installed, your username should be the username you logged in with on your mac

```sql
ALTER USER postgres SET TIMEZONE='UTC';
```

After above command, if you run `SHOW TIMEZONE`, you still see `UTC+1`. To fix this, kill all of your connections and then reconnect to DB.  
Then run SHOW TIMEZONE again.

With this, all of our future sessions(not only this session) will be UTC.

## 88-88 - How Do We Format Date And Time
What does UTC look like? Or how do dates look?

Postgres uses ISO-8601 which is a standard that tells us how we should format dates and times(show them like this, process them like this).

### ISO-8601 format for datetime:

YYYY-MM-DDTHH:MM:SS

2017-08-17T12:47:16+02:00

+02:00 means the timezone is a plus 2 hour offset from UTC. This part is optional because if you don't give it, it will assume that it is already in UTC, so
it doesn't have a timezone, it's UTC format.

Not giving a timezone to sth, assumes that it's already UTC.

UTC is a time standard, ISO-8601 is a formatting standard, it's a way to depict a UTC date and time in a uniform way. Postgres at it's core uses UTC and
the way postgres will store date formats is in the standard of ISO-8601.

A format is a way of representing a date and time.

### Timestamp:
A timestamp is a date with time and (potentially) timezone info.

## 89-89 - Timestamps
```sql
SELECT now();
```

With above query, we get a ISO-8601 formatted string potentially with the timezone information depending on timezone set on your postgres installation. 

Timestamps are utilized to store more granular info about our date. Say you wanted to store the last time someone logged in. In that case, you would want to
store the timezone info and ... .

EX) example of timestamp:

1800-01-01 00:00:00.00 -5

YYYY-MM-DD HH:MI:SS.MS T

There are 2 different ways of storing timestamps:

```sql
CREATE TABLE timezones (
    ts TIMESTAMP WITHOUT TIME ZONE,
    tz TIMESTAMP WITH TIME ZONE,
)
```

2 nuances with timestamps:
1) the one with `WITH TIME ZONE` has extra info which is the timezone itself(`WITHOUT TIME ZONE` doesn't store timezone).
2) the one with `WITHOUT TIME ZONE` doesn't offset the time with timezone that you entered, according to that timezone. Instead, it just stores the time
and won't look at the provided timezone. For example if you enter: '2000-01-01 10:00:00-05' in a column with `TIMESTAMP WITH TIME ZONE`, it will ignore the timezone
and won't offset the time you entered. So it acts if the time we're giving it is already UTC(even if it isn't! So even if you specified a timezone with some offset
which means it's not UTC!) But when we're inserting time into a table that has `WITH TIME ZONE`, we're saying: hey postgres, this has this timezone, so make
sure that when you store it(it's gonna store times in UTC by default), make sure you take into account the timezone(which means it will offset the time according to
given timezone).

So when we insert timestamp info, we can insert it in a way that tells postgres: Hey ignore the timezone or take into account timezone which means it will offset
the time.

### Use timestamps or dates?
It depends on what you're storing. For the majority of the cases, we often use dates. Because the granular info(time and timezones) that we require isn't necessary.

It's easy to go from a date to a timestamp and vice versa.

## 90-90 - Date Functions
### Current date
To get current date, there are 2 main ways:
```sql
SELECT NOW()::date;
SELECT CURRENT_DATE;
```
These will give you a formatted subset of the ISO-8601. Sth like: 1800-01-01 .

### Formatting
```sql
SELECT TO_CHAR(CURRENT_DATE, 'dd/mm/yyyy');
```
With TO_CHAR() we can format a date.

In europe is common to use slashes and it's first day and month and then year.

### Format modifiers
Each modifier has an identifier. D is for day, M for month and Y for year.

## 91-91 - Date Difference And Casting
We want to calculate the different between two dates.

```sql
SELECT now() - '1800/01/01';
```
With above, postgres will return an interval(there is no name for the returned column). The result is:

?column?

<no> days: HH:MI:SS.MS

**Subtracting dates returns the difference in days.**

### To date
```sql
SELECT date '1800/01/01';
```
Returns:

?column?

1800-01-01

The above, converts a text to a date format for postgres which is ISO-8601.

If you put `date` in front of a string(before a text), it will consider it a date. It will also format it at the same time.w

Interesting, because 1800/01/01 is not valid syntax for the ISO-8601, because it doesn't use slashes. But when we cast it to a date,
it's gonna cast it to a ISO-8601 date. It converts the date to a date format for postgres which is ISO-8601 that uses dashes instead of slashes.

## 92-92 - Age Calculation
### Calculate age:
This is wrong ❌:
```sql
SELECT AGE('1800/01/01');
```
Returns:

?column?

ERROR

When we calculate age, we need to pass it a valid date and not a string(we need to case string to date):

Correct ✅:

```sql
SELECT AGE(date '1800/01/01');
```

Returns:

?column?

<no> years <no> months <no> days

### Calculate age between 2 dates
```sql
SELECT AGE(date '1992/11/13', date '1800/01/01');
```

Returns:

192years 10months 122 days

AGE() returns an interval so we may want to cast the result to sth else like the amount of days. For this, you can use TO_CHAR() on the result.

## 93-93 - Extracting Information
### Extract day:
```sql
SELECT EXTRACT(DAY FROM date '1992/11/13') AS DAY; -- you can use MONTH and YEAR too
```

### Round a date:
For example if you don't care about month and day(set them to lowest possible values). Like getting everything from the beginning of the month and
end of the month. So we can do a DATE_TRUNC() against the month of the CURRENT_DATE.

For example:
```sql
SELECT DATE_TRUNC('year', date '1992/11/13');
```
Result:

?column?

1992-01-01

```sql
SELECT DATE_TRUNC('month', date '1992/11/13');
```
Result:

?column?

1992-11-01

**BUT:**

If you pass day to DATE_TRUNC() , it's not gonna do anything to it(the result is the same as the date input), unless you give it a timestamp which it will
set all of the values in the timestamp to 0(only the respective things). So with timestamp, we have more granular control over truncation.

```sql
SELECT DATE_TRUNC('day', date '1992/11/13');
```
Result:

?column?

1992-11-13

## 94-94 - Intervals
### Interval
Find the date of purchase for last 30 days:
```sql
SELECT *
FROM orders
WHERE purchaseDate <= now() - interval '30 days' -- TODO: Shouldn't his be purchaseDate >= now() ...
```

Returns:

<30 days before given date>

Interval allows us to write queries in a way that mirrors language. It can store and manipulate a period of time in years, months, days, hours, minutes,
seconds, etc.

```sql
SELECT EXTRACT(year FROM INTERVAL '5 years 20 months');
```
Returns: 6. Because 20 months is more than a year.

**Note:** When you want to do manipulations with dates towards the future or past, you should use INTERVAL. 

## 95-95 - Exercise Date and Timestamp
File

## 96-96 - DISTINCT
### Remove duplicates:
Distinct clause keeps one row for each group of duplicates.

```sql
SELECT DISTINCT <col1>, <col2> FROM <table>;
```

Using distinct with multiple columns after DISTINCT keyword vs just 1 column for DISTINCT, possibly returns more rows because
when using multiple columns for DISTINCT, it will only remove the rows that have duplicate items for ALL of the specified columns.

## 97-97 - Exercise Distinct Keyword
File
## 98-98 - Sorting Data
When passing multiple columns to ORDER BY, THE ASC or DESC applies to the exact column it was specified for, not all of the columns:
```sql
SELECT first_name, last_name FROM employees ORDER BY first_name, last_name DESC;
```
In above example, first_name would be ordered ascending(default one) and last_name descending.

**Note:** The relational model doesn't give you results based on a specific order unless explicitly asked for.

## 99-99 - Exercise Sorting Data
File

## 100-100 - Multi Table SELECT
```sql
SELECT a.emp_no,
       CONCAT(a.first_name, a.last_name) as "name",
       b.salary
FROM employeees as a, salariees as b
```
The result would be we assign every single salary from the salary table to every single individual, multiple times.
So for example a person would have multiple salaries in the result. Why? Why is it adding so many salaries tto 1 person?

Instead of having repeated results, let's filter them using WHERE clause
```sql
SELECT a.emp_no,
       CONCAT(a.first_name, a.last_name) as "name",
       b.salary
FROM employeees as a, salariees as b
WHERE a.emp_no = b.emp_no -- <------
```

When selecting from multiple tables oro a table with itself, it's important to create a link between those tables, otherwise for each record
from the other table, it's gonna add it to each record from the original table!

The most common approach of joining, is to link the primary key to the foreign key.

## 101-101 - Inner Join
```sql
SELECT a.emp_no,
       CONCAT(a.first_name, a.last_name) as "name",
       b.salary
FROM employeees as a,
INNER JOIN salariees as b ON a.emp_no = b.emp_no
```

**Note:** When you do a join, you have to decide what's going to be on the left side and what's going to be on the right side. Meaning 
where am I selecting from and what I'm trying to join it with.

**Note:** The table we want to select FROM, is the main part of the data and the table we want to JOIN it with, is the secondary part.

For example when we want to join employees to salaries, the employees table is main and salaries table is secondary table, so we select FROM employee
and JOIN it with salary table(so `INNER JOIN salaries ...`).

So we saw 2 approaches for selecting from multiple tables: using WHERE clause and using INNER JOIN. The INNER JOIN syntax is generally considered
a best practice. It's more readable than using the where syntax. It shows what's being joined.

## 102-102 - Self Join
Joining a table to itself.

This usually can be done when a table has a foreign key referencing it's primary key.

With supervisorId, we're specifying who is someone else's manager? So the table could be like this:

![](../img/102-102-1.png)

This is an uncommon scenario because normally you would have some kind of manager table that would keep references to these things,
but in some tables it's easier to keep a reference to itself.

What if you wanted to see the supervisors name?
```sql
SELECT a.id, a.name as "employee", b.name as "supervisor name" FROM employee as a, employee as b 
WHERE a.supervisorId = b.id;
```
We're selecting from the **same** table twice.

Self join is joining a table to itself because a primary key and a foreign key are in the same exact table. So for doing this, we need a 
primary key and a foreign key in the same exact table that reference the same exact information(in this case supervisorId being a foreign key
and to id column(primary key)).

Using inner join:
```sql
SELECT a.id, a.name as "employee", b.name as "supervisor name" FROM employee as a
INNER JOIN employees as b
ON a.supervisorId = b.id;
```

Self join is just a variant of inner join where you join a table to itself.

## 103-103 - Outer Join
What if I also need the rows that don't match?

Outer joins add the data that don't have a match.

2 types of outer joins:

When we talk about the left hand side join, we're talking about the table that is in the FROM statement and when we talk about the right side of
the join, we're talking about the table that is being joined.

LEFT [OUTER] ...

Any value that does not match, will be null.

How many employees aren't managers?

Note: left outer join goes against the table selected in FROM statement, the right outer join goes against the table that is being joined(table
that is in the JOIN statement).

## 104-104 - Less Common Joins
https://www.db-fiddle.com/f/dAb6mjWqWay6ECY1o2v478/0

## 105-105 - InnerJoin Exercises
## 106-106 - USING Keyword