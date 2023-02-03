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

### 112-112 - Group By Mental Model