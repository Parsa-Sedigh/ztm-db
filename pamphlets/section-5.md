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
multiple outputs. These functions operate oon each record independently. Like concat() function.

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

## 76-76 - NULL Coalescing
## 77-77 - Exercise Null Value Coalescing
File
## 78-78 - 3 Valued Logic
## 79-79 - Exercise 3 Valued Logic
File
## 80-80 - BETWEEN AND
## 81-81 - Exercise BETWEEN AND
File
## 82-82 - IN Keyword
## 83-83 - Exercise IN Keyword
File
## 84-84 - LIKE
## 85-85 - Exercise Like Keyword
## 86-86 - Dates And Timezones
## 87-87 - Setting Up Timezones
## 88-88 - How Do We Format Date And Time
## 89-89 - Timestamps
## 90-90 - Date Functions