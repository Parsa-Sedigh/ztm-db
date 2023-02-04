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

For a window function to be appliedxas a window function, you need that `OVER` keyword. Because it's gonna tell you: hey, apply this
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

## 120-120 - Solving For Current Salary
## 121-121 - FIRSTVALUE
## 122-122 - LASTVALUE
## 123-123 - SUM
## 124-124 - ROWNUMBER
## 125-125 - Window Function Exercises
File
## 126-126 - Conditional Statements
## 127-127 - Conditional Statement Exercise
File
## 128-128 - NULLIF
## 129-129 - NULLIF Exercise
File
## 130-130 - ViewsWhat Are They Good For
## 131-131 - View Syntax
## 132-132 - Using Views
## 133-133 - Views Exercises
File
## 134-134 - Indexes


### 134 - Poor indexing a performance killer
https://www.sqlshack.com/poor-database-indexing-sql-query-performance-killer-recommendations/

## 135-135 - Index Types

### 135 - Index Types
https://www.postgresql.org/docs/12/indexes-types.html

## 136-136 - Index Algorithms
## 137-137 - Quick Note On Index Algorithms
File
## 138-138 - What Are Subqueries
## 139-139 - Subqueries vs Joins
## 140-140 - Subquery Guidelines As Types
## 141-141 - Using Subqueries
## 142-142 - Quick Note Titles For Employees
File
## 143-143 - Getting The Latest Salaries
## 144-144 - Subquery Operators
## 145-145 - Subquery Exercises
File