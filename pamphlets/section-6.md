# Section 6: 6 - Advanced SQL

## 107-107 - GROUP BY
Grouping data into logical buckets, allows us to operate against those buckets individually.

When you run sth like:
```sql
SELECT dept_no, COUNT(emp_no) FROM dept_emp;
```
we will get an error that says:
`ERROR: column "dept_emp.dept_no" must appear in the GROUP BY" caluse or be used in an aggregate function`

Didn't we learn that SUM, MIN, MAX, COUNT return 1 record? They aggregate right? So if they're returning 1 record, what is `GROUP BY` for?
Why is it complaining about `GROUP BY`?

`GROUP BY` splits data into groups or chunks, so we can apply functions against the group rather than the entire table. 
The purpose of group by is to run functions against some **other** columns than the columns used in `GROUP BY`. For example, here we're grouping by
dept_no, but we run a function against some other column than the one used in GROUP BY(I mean against emp_no which is not used in `GROUP BY`):
```sql
SELECT dept_no, COUNT(emp_no)
FROM dept_emp
GROUP BY dept_no; 
```
With this, we get the number of employees in each department.

We use `GROUP BY` almost exclusively with aggregate functions.

Why?

Because when we get a group of data, we often want to know sth about that group and that sth which we want to know, is most likely a COUNT or SUM or MIN or ... .

When we `GROUP BY`, we apply the function per group, not on the entire data set.

### Things to remember:
`GROUP BY` is stricter than it looks

Every column not in the `GROUP BY` clause, must apply a function. So in the previous example, if we don't apply an aggregate function to emp_no, we will get an error.

Why?

To reduce all records found for the matching group, to a single record. In other words, we know that GROUP BY reduces all records found for the matching group,
to a **single** record and we can run analytical functions against columns other than the ones in GROUP BY clause. So you have to either use an aggregate
function to boil itself down to one record, or it needs to be part of the grouping(it has to be used to create that group).

### How does it work?
GROUP BY utilizes a split-apply-combine strategy.

Here, we're grouping by x(GROUP BY X). So we're gonna split the unique groups of x(each groups should have records with the same value for the column we used it
in GROUP BY). Then apply an aggregate function to the columns **other than** the one used for GROUP BY(in this case, that column is **y**). Then combine the results:
![](../img/107-107-1.png)

### Split phase
Divide into groups with values
### Apply phase
Apply aggregate against ungrouped columns(columns that have a function applied on them)
### Combine phase
Combines groups with a single value into single value

## 108-108 - Group By Exercises

## 109-109 - HAVING Keyword:
What if I want to filter groups?

```sql
SELECT col1, COUNT(col2)
FROM <table>
WHERE col2 > X
GROUP BY col1
HAVING col1 === Y;
```

`WHERE` applies filters to individual rows but `HAVING` applies filters to a group as a whole and not only the groups as a whole but also
when we apply aggregate functions against a group, the HAVING clause can filter against the **output** of that aggregate function.

**Note:** Aggregate functions are not allowed in `WHERE` and the HAVING clause exists because of this reason. It's entire purpose is to filter
against the groups.

## 110-110 - Having Exercises

## 111-111 - Ordering Grouped Data
ORDER BY happens after GROUP BY, but you can use aggregate functions to order by the output of those functions.

## 112-112 - Group By Mental Model
When you GROUP BY multiple columns, each row would have a unique combination of those columns. So you may get results that have same value for
some of the columns used in GROUP By, but at least one of the columns would differ.

## 113-113 - Grouping Sets
What if we want to combine the results of multiple groupings?

### Union
Combining results of multiple selects:
```sql
SELECT col1, SUM(col2)
FROM table
GROUP BY col1
UNION
SELECT SUM(col2) -- this will get us the total sum of all the records(whole table, because we didn't use GROUP BY)
FROM table;
```

We could use `UNION ALL` in the previous example too. What's the difference?

The main difference is that `UNION ALL` does not remove duplicate records.

Approach 1:
```sql
SELECT NULL AS "prod_id", sum(ol.quantity)
FROM orderlines AS ol

UNION

SELECT prod_id AS "prod_id", sum(ol.quantity)
FROM orderlines AS ol
GROUP BY prod_id
ORDER BY prod_id DESC;
```

Approach 2(with 1 query instead of 2): With GROUP BY, we can combine the outputs of multiple groupings.
```sql
SELECT prod_id AS "prod_id", sum(ol.quantity)
FROM orderlines AS ol
GROUP BY 
    GROUPING SETS (
        (),
        (prod_id)
    )
ORDER BY prod_id DESC;
```

So `GROUPING SETS()` is another way of writing UNION syntax which had 2 queries.

### GROUPING SETS
A sub-clause of GROUP BY that allows you to define multiple groupings.

### 113-113 - exercises
https://github.com/mobinni/Complete-SQL-Database-Bootcamp-Zero-to-Mastery/blob/master/SQL%20Deep%20Dive/Grouping%20Sets/exercises.sql

Calculate the total amount of employees per department using grouping sets
Database: Employees 
Table: Employees
```sql
select grouping(e.dept_no), e.dept_no, COUNT(e.emp_no)
FROM public.dept_emp as e
GROUP BY
GROUPING SETS (
(e.dept_no),
()
)
order by e.dept_no
```

Calculate the total average salary per department and the total using grouping sets
Database: Employees
Table: Employees
```sql
select grouping(de.dept_no), de.dept_no, AVG(e.salary)
   FROM public.salaries as e
   JOIN public.dept_emp as de USING (emp_no)
   GROUP BY
   GROUPING SETS (
   (de.dept_no),
   ()
   )
   order by de.dept_no
```

## 114-114 - Rollup
How much did I sell on each year, month and day?

Answer:

We want to know each individual day by month and year and then we want to know each individual month by year and then we want to know
each individual year, of the total quantity.

Note: In this example, we did not write all of the different combinations in the grouping set! 
```sql
SELECT extract(YEAR FROM orderdate) AS "year",
SELECT extract(MONTH FROM orderdate) AS "month",
SELECT extract(DAY FROM orderdate) AS "day",
       sum(ol.quantity)
FROM orderlines AS ol
GROUP BY 
    GROUPING SETS (
        (extract(YEAR FROM orderdate)),
        (
            extract(YEAR FROM orderdate),
            extract(MONTH FROM orderdate)
        ),
        (
            extract(YEAR FROM orderdate),
            extract(MONTH FROM orderdate),
            extract(DAY FROM orderdate)
        ),
        (
            extract(MONTH FROM orderdate),
            extract(DAY FROM orderdate)
        ),
        (extract(MONTH FROM orderdate)),
        (extract(DAY FROM orderdate)),
        () -- for calculating the total
    )
ORDER BY
    extract(YEAR FROM orderdate),
    extract(MONTH FROM orderdate),
    extract(DAY FROM orderdate)
```

With this query, we have multiple different groups.

But this is a lot of work because we have to type out all the groupings that we want.

There's an easier way and it's called ROLLUP. **It's gonna create every possible combination of the things you pass to it(dynamically
creates different groupings including the non-group which applies functions to whole table).** 
```sql
SELECT extract(YEAR FROM orderdate) AS "year",
SELECT extract(MONTH FROM orderdate) AS "month",
SELECT extract(DAY FROM orderdate) AS "day",
       sum(ol.quantity)
FROM orderlines AS ol
GROUP BY
    ROLLUP(
        extract(YEAR FROM orderdate),
        extract(MONTH FROM orderdate),
        extract(DAY FROM orderdate)
    )
ORDER BY
    extract(YEAR FROM orderdate),
    extract(MONTH FROM orderdate),
    extract(DAY FROM orderdate)
```

`ROLLUP` is useful when you have grouping sets that need all of the possible combinations.

### 114 - Complex Groupings
https://www.postgresql.org/docs/12/queries-table-expressions.html#QUERIES-GROUPING-SETS

## 115-115 - Window What
### What we learned so far
1. grouping data is useful: GROUP BY allows us to apply aggregate functions against groups(chunks) of data rather than applying on the entire
table
2. grouping happens after FROM and `WHERE`
3. `HAVING` is a special filter for groups that acts like `WHERE` but it's for groups
4. `GROUPING SETS` and `ROLLUP`s are useful for multiple groupings in a single query
5. grouping data is not a silver bullet

### What are we missing?
How do we apply functions against a set of rows(like a group) related to the current row?

For example:

What if we wanted to know the average salary per department?

Easy peasy:
```sql
SELECT d.dept_name, ROUND(AVG(salary))
FROM salaries
JOIN dept_emp as de USING(emp_no)
JOIN departments as d USING(emp_no)
GROUP BY dept_no, dept_name;
```
What if we want to: 

Add the average salary to every salary so we could visually see how much each employee is from the average.

What we do in this case?

We should use window functions.

## 116-116 - Looking Through The Window
https://www.postgresql.org/docs/12/tutorial-window.html

Window functions create a new column based on functions performed on a subset or **"window"** of the data.
`
window_function(arg1, arg2...) OVER (
    [PARTITION BY partition_expression]
    [ORDER BY sort_expression] [ASC | DESC] [NULLS { FIRST | LAST }]
)
`

For a window function to be applied as a window function, you need that `OVER` keyword. Because it's gonna tell you: hey, apply this
function **over** this window(subset) of data.

If you only say: `OVER()`, it will apply it against the **entire** window of data that you requested. The window function applies against each
individual row of the window of data.

Note: The window function applies to window of data **before** some stuff like LIMIT and OFFSET and ... are applied(but **after** the filters like
WHERE is applied). Because when we use LIMIT, OFFSET, the query **still** returns everything without applying that LIMIT and OFFSET and LIMIT and OFFSET
just cut some data off from the returned result.

## 117-117 - PARTITION BY
PARTITION BY: Divide rows into groups to apply the function against(optional)

`
AVG(s.salary) OVER() as "average global salary"
AVG(s.salary) OVER(
    PARTITION BY d.dept_name
) as "average salary for each dept_name group"
`
In second one, our window function generates average salary per dept_name and by partitioning, we returned both the column that was portioned by and
the result of applying the window function on that group. So in the second case, the dept_name column would also be returned with another column
named avg which has the result of avg() on that group.

**Note:** PARTITION BY also returns the columns that we pass to it.

## 118-118 - Order By Acting Strange
Within a window function, ORDER BY operates kinda strangely.

ORDER BY has a property called framing. ORDER BY changes the frame of the window function.

When you use ORDER BY in a window function, you're telling your query and the window function used inside that query to
take into account everything before me and myself included.

## 119-119 - Using Framing In Window Function
When using a frame clause in a window function, we can create a sub-range or frame.

When we use PARTITION BY, we're creating chunks.

![](../img/119-119-1.png)

**Note:** Without ORDER BY, by default the framing is usually all partition rows(the entire partition). 
IF you use `OVER()` without anything in it, the entire partition is the entire window.

But when we introduce `ORDER BY`:

With `ORDER BY`, by default the framing is usually everything before the current row and the current row.

When using `PARTITION BY` with `ORDER BY` using the **same columns**, the frame is gonna be solely that partition itself 
and it won't look at all the rows before that partition: For example:
```sql
SELECT emp_no, salary, COUNT(salary) OVER (
        PARTITION BY emp_no
        ORDER BY emp_no
    )
```

Example: In this example, we're partitioning by emp_no, so all the same emp_no will be grouped together as you see. But since we're
ordering by salary and the ORDER BY is used inside a window function(COUNT()), the COUNT() window function is gonna look at itself and
what came before it(since the columns used in PARTITION BY and ORDER BY are different - though you can change this behavior) and since
salaries are unique values, the count col is incremented by 1 because there is only 1 record in each partition.
![](../img/119-119-2.png)

So the way you `PARTITION BY` and the way you `ORDER BY`, influence each other.

**Note:** By default, if ORDER BY is supplied then the frame consists of all rows from the start of the partition up through the
current row, plus any following rows that are equal to the current row according to the ORDER BY clause. 
When ORDER BY is omitted the default frame consists of all rows in the partition.

## 120-120 - Solving For Current Salary
Q: Get the current salary of an employee.

A:
![](../img/120-120-1.png)

Visualization for the last query:
![](../img/121-121-1.png)
## 121-121 - FIRSTVALUE
Return a value evaluated against the first row within it's partition.

Q: I want to know how my price compares to the item with the lowest price in the same category.
![](../img/121-121-2.png)

Approach 2(easier):
```sql
SELECT prod_id, price, category, min(price) OVER(
        PARTITION BY category
    ) AS "cheapest in category"
FROM products;
```

## 122-122 - LASTVALUE
Return a value evaluated against the last row within it's partition.

I want to know how my price compares to the item with the highest price in the same category.
![](../img/122-122-1.png)
In the last example, we changed the framing to be RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING.

Better approach would be to use max() function:
```sql
SELECT prod_id, price, category
MAX(price) OVER (
    PARTITION BY CATEGORY
)
AS "most expensive in category"
```

## 123-123 - SUM
Sum the values within a group depending on the framing.

I want to see how much cumulatively a customer has ordered at our store.
![](../img/123-123-1.png)

`ORDER BY` is important to be here, because we want to sum **up to that point**. If we don't use `ORDER BY`
here, we won't get the result that the question wants. So if we don't use ORDER BY, we will get the total sum
in that whole partition and not a cumulative sum to that row in the partition. So that's where `ORDER BY` can
change how things work.

## 124-124 - ROWNUMBER
Number the current row within the partition starting from 1 regardless of framing.

I want to know where my product is positioned in the category by price.
![](../img/124-124-1.png)

## 125-125 - Window Function Exercises
File
## 126-126 - Conditional Statements
### Conditionals
What if you only want to select sth when a certain criteria is met?

Although with the WHERE clause we can filter and meet some criteria to get sth out, a conditional is a different way of doing the same thing,
but you can use it in multiple places in a query.
### Case statement:
```sql
SELECT a,
       CASE WHEN a=1 THEN 'one'
            WHEN a=2 THEN 'two'
            ELSE 'other'
       END
FROM test;
```

CASE statements can be used in multiple places in a query.

With CASE statement we can say: if a is this, return this, if a is that return sth else, if it's 1 return sth else and ... .

Each return must be a single output.

![](../img/126-126-1.png)

Conditional filter:
![](../img/126-126-2.png)
We can do the previous example using ANDs and ORs.

![](../img/126-126-3.png)

## 127-127 - Conditional Statement Exercise
File
## 128-128 - NULLIF
What if you want to return NULL if a condition is met?
![](../img/128-128-1.png)
If `val1` and `val2` are not equal, it will return `val1`.

Fill in empty spots with a NULL to avoid divide by zero issues(divide by null returns null) and other issues.

## 129-129 - NULLIF Exercise
File
## 130-130 - ViewsWhat Are They Good For
What if I want to store the results of a query? What if I want to query the results of a query?

Views allow you to store and query previously run queries.

There are 2 types of views: Materialized and non-materialized

### Non-materialized views:
Query gets re-run each time the view is called on.

We're storing a reference to the query and we're re-running the query each time we call the view, so we're not storing the results of query,
we're just storing a reference to the query itself.

### Materialized views:
Stores the data physically and periodically updates it when tables change.

## 131-131 - View Syntax
### Creating a view:
```sql
CREATE VIEW <view_name> AS <query>;
```

Views are the output of the query we ran.

Views act like tables, so you can query them.

About non-materialized view:

Views take very little space to store. We only store the definition of a view, not all of the data that it returns.

But when we talk about materialized views, we're storing the output of the data physically.

The majority of time you will want to work with non-materialized views, there are some instances in which you want to keep materialized view
around for whatever reason, performance reasons, you don't want to constantly have to rerun that query, you want to update it in the background
because it's heavy.

### Updating a view:
```sql
CREATE OR REPLACE <view_name> AS query;
```

### Rename a view:
```sql
ALTER VIEW <view_name> RENAME TO <view_name>;
```

### Deleting a view:
```sql
DROP VIEW [IF EXISTS] <view_name>;
```

With `IF EXISTS`, it won't throw an error if the view doesn't exist.

## 132-132 - Using Views
### Current salary:
Getting the most recent salary of an employee:

GROUP BY didn't really give us a correct outcome. Because in the solution below with `GROUP BY`, we assumed the salary of employee is always
increasing, butt this assumption isi wrong.
![](../img/132-132-1.png)

Using window functions we could pull off some voodooo magic. But they need heavy computational needs, they taeke time, because they run against
each individual record.
![](../img/132-132-2.png)
But the solution above isa not really intuitive, so how about we try views?

The solution using views:
![](../img/132-132-3.png)
We used MAX(s.from_date) to get the last recent salary.

Now that you have created that view, we can query it like it is a table!
```sql
SELECT * FROM last_salary_change;
```

Knowing this, we can now run this query to answer the question:
```sql
SELECT * FROM salaries
JOIN last_salary_change AS l USING (emp_no)
WHERE from_date = l.max -- the max column is the result of calling MAX() function
ORDER BY emp_no;
```

So instead of using a window function, we wrote a simple query with join syntax and a filter.
![](../img/132-132-4.png)

In previous solution(solution using views), we're comparing view's `max` column with salaries fromm_date column.

Pro: Join syntax with filtering is easier to read.

Pro: It's easier to reason about.

Drawback: You have to create a view.

## 133-133 - Views Exercises
File

## 134-134 - Indexes
What is an index?

An index is a construct to improve querying performance. It's a pointer to data in a table.

Think of it like a table of contents, it helps you find where a piece of data is.

Slows down data insertion and updates.

### Types oof indexes   :
- single-column
- multi-column
- unique
- partial
- implicit indexes: Things that automatically will be indexed because postgres, mysql ... gonna index it by default.

### Creating an index:
```sql
CREATE [UNIQUE] INDEX <name> ON <table> USING <method> (<column1>, <column2>, ...);
```

### Deleting an index:
```sql
DROP INDEX <name>;
```

### When to use:
- index foreign keys
- index primary keys and unique columns
- index on columns that end up in the `ORDER BY` / `WHERE` causes often

Consider using a naming convention for indexes and other stuff!

### When NOT to use:
- don't add an index just to add an index(don't just throw indexes around!)
- don't use indexes on small tables(small number of records)
- don't use on tables that are updated frequently. Because the system has to spend time re-indexing and re-indexing and ... .
- don't use on columns that can contain null values
- don't use on columns that have large values

### Poor indexing a performance killer
https://www.sqlshack.com/poor-database-indexing-sql-query-performance-killer-recommendations/

## 135-135 - Index Types
### single-column:
Indexes that are used against the most frequently used **column** in a query.

The most frequently used column is often the column that you're referring to, in every single query, like in WHERE clause.

### Multi-column:
Most frequently used **columns** in a query.

### When to use:
Single-column: If you're retrieving data that satisfies one condition.

Multi-column: If you're retrieving data that satisfies multiple conditions(a `WHERE` clause with ANDs or ORs).

### Unique:
For speed and integrity
```sql
CREATE UNIQUE INDEX <name> ON <table> (<column1>);
```

### Partial
Index over a subset of a table
```sql
CREATE INDEX <name> ON <table> (<expression>);
```

### Implicit:
Implicit indexes are automatically created by the database. When does it create them?
- primary key
- unique key

Example for creating a partial index in World DB and in it's city table:
```sql
CREATE INDEX idx_countrycode
ON city (countrycode) WHERE countrycode IN ('TUN', 'BE', 'NL');
```
It's gonna index the ones that appear in the list we gave to `IN`. Now if you query has only those columns in for example it's WHERE clause,
it's gonna be fast.

`name` is a reserved word in sql, so you need to put it between double quotes in queries if you have a column named `name`.

Put `EXPLAIN ANALYZE <query>` to see a explain result for that query.

In partial index, part of the query is gonna be indexed.

### Index Types
https://www.postgresql.org/docs/12/indexes-types.html

## 136-136 - Index Algorithms
### Index algorithms:
PostgreSQL provides several types of indexes:
- B-tree
- hash
- gin
- gist

Each index type uses a different algorithm.

B-tree is the default algorithm.

When should I use which algorithm?

### B-tree
Default algorithm! Best used for comparisons with: <, <=, =, >=, Between, IN, IS NULL, IS NOT NULL

### hash
Can only handle equality(=) operations.

Hash algorithm is faster than B-tree (of course in equality comparisons).

### gin
Generalized inverted index

Best used when multiple values are stored in a single field.

### gist
Generalized search tree

Useful in indexing geometric data and full-text search

## 137-137 - Quick Note On Index Algorithms
In the previous video you saw us using an index algorithm there was a small typo in the query that may cause some confusion.

The `WHERE` clause in one of the queries (at the 6 minute mark) was using the AND keyword for the country code check, this 
should have been OR as there is no possibility for the country code to match all of these values but only one at a time.

## 138-138 - What Are Subqueries
### What is a subquery
A construct that allows you to build extremely complex queries.

A subquery is also called an inner query or inner select.

It's a query within another SQL query most often found in the WHERE clause.

In the subquery, we should only return a single column if it's used in a `WHERE` clause of a parent query. So a subquery used in a 
`WHERE` clause, it should only return a single column not multiple columns.

**Note:** You can also use a sub query in the `SELECT`, `FROM` and `HAVING` clause(and `WHERE` clause).

For example if you want to use a sub-query in `SELECT` statement of parent query:
![](../img/138-138-1.png)
So when you use a sub-query in a select statement, the subquery must return a single record.

Use a sub-query in `FROM` clause:
![](../img/138-138-2.png)

Using a sub-query in `HAVING` clause:
![](../img/138-138-3.png)

**Heads up:** since you're returning a single record, you usually, gonna select 1 column in the subquery when used in HAVING and SELECT part of it's
parent query.

So if you use it to query different tables ... How is it(subquery) different from a JOIN?

Let's look at some of the differences between a JOIN and a sub-query in the next video.

## 139-139 - Subqueries vs Joins
Both sub queries and joins combine data from different tables. But sub queries are queries that could stand alone.

Joins combine rows from one or more tables based on a match condition.

Sub queries can return a single result or a row set. 

Joins can only return a row set.

A sub query's results are immediately used. Whereas a joined table can be used in the outer query. In other words, in different parts of a
query, we can reference parts of the join(like the table we joined with), but we can't do this with sub query(we can't reference it's data or
table in other parts of the query).

Deciding when to use a sub-query vs when to use a join is a difficult choice.

**Hint:** If you're able to use a join, always use a join! Because generally they will be more performant than sub-queries because 
sub-queries still need to execute that query.

## 140-140 - Subquery Guidelines As Types


## 141-141 - Using Subqueries
## 142-142 - Quick Note Titles For Employees
File

## 143-143 - Getting The Latest Salaries

## 144-144 - Subquery Operators

## 145-145 - Subquery Exercises
File