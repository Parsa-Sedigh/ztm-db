# Section 7 - 7 - Database Management

## 146-146 - Before We Get Started
Throughout this section, you will find that we are taking a slightly different approach.

When we approached querying it was beneficial to learn the concepts and what they do, applying them to certain scenarios.

Doing all that before touching on exercises builds a solid foundation for applying knowledge.

However, there are many different strategies to employ effective learning, throughout this bootcamp we’ve enjoyed
everything from history to theory and practical exercises. In this section, you will find a healthy mix.

From the get-go our plan was to build a solid foundation for you to start off your SQL mastery, as with any technology 
there are multiple avenues to walk when exploring SQL.

The avenue of data creation and management is one that often crosses the realm of architecture and administrator. 
You see on the one hand you have the definition and creation of databases and the relationships between different tables and on the other hand, you have the management of roles, permissions, backups, and so forth.

Very different skills indeed, nevertheless extremely valuable to know and use, SQL is so much more than just
querying data - it’s a lifestyle! I joke, but I also digress.

Throughout this section what you will find is a healthy mix of theory and practical application when it comes to
management and the principles, as well as a practical example of database architecture and creation.

There are many commands to master in this section, but the reality is once you go out there and start applying these 
skills that’s when the real learning will start, the foundation this course gives you sets you up for a life long journey of fun problem-solving with SQL!

## 147-147 - Time To Create Some Stuff
### What we'll be learning
- the different types of data
- how to create a database
- how to create tables
- how to insert/update/... data
- ... so much more

We're gonna look at DML(data modification language) and DDL(data definition language).

### Data definition language
Commands to create, modify, delete different **structures**. Structures means databases, tables, roles(role management).

### Data manipulation language
Commandos for adding, deleting, modifying **data**(we're looking at more granular view).
![](../img/147-147-1.png)

## 148-148 - Types Of Databases In A RDBMS
### Creating a database
### Types of databases
Types of databases that are available when you look at database management systems

- regular
- template

When you setup your postgres, 3 databasess were created:
- postgres
- template0
- template1

## 149-149 - Default PostgreSQL Database
Why the postgres database created when we setup postgres?

### Postgres database
This is the default database that is created when yoou setup postgres(INITDB)

So the first time you initialize postgres, you can connect to the postgres database to setup your first database.

With this, we can connect to the specified <database>.
```shell
psql -U <user> <database>
```
But what if we don't give it a database?

Postgres by default will assume a connection to a database with the same name as the user, if no database is supplied.
```shell
psql -U postgres # this will assume you're trying to connect to a database called `postgres` because the username is postgres
```

Whoever would do this?

No one! You always give a database name, when you're connecting. Because if you're not logged in as postgres user, you may not have a database
with the same name as your user.

`
postgres-# \conninfo
You are connected to database "postgres" as user "postgres" via socket in "/tmp" at port "5432".
`
This means we connected and we went into postgres database and we ran the command: \conninfo, we connected without giving it a database name,
it would connect to the database with the same name of user.

**Tip:** `\conninfo` shows the current connection.

## 150-150 - Template Databases
Template databases are used to be a blueprint for regular databases.

### Template0 database
This is the template that is used to create template1 - never change it(template0)!

In essence, it means it's a backup for template1. So it's in essence, a backup template.

### Template1 database
This is the template that is used to create new databases.

So if you run: `CREATE DATABASE` command, it's going to use template1, unless you tell it to use sth else.

Any changes that you make to template1 database, are automatically applied to all new databases(not the old ones).

So be careful with what you change there. Instead, create different templates if you want to play around with it.

One thing to note is that because template1 is the default template, if it's being accessed/connected to, no new databases can be created
until the connection is closed!

```shell
psql -U postgres template1
# then the prompt would be: template1=#

# now run:
\conninfo
```

Now open up a new terminal window while the previous one is still running:
```shell
psql -U postgres Emplooyees # connect to Employees database

# create a new database
CREATE DATABASE myblockeddb;

# now it's hanging and then throws an error:
ERROR: source database "template1" is being accessed by other users
DETAIL: There is 1 other session using the database.
```

Now if you in the shell session that is connected to template1, run `exit`, then if you ran the CREATE DATABASE command again, it works.

Now if the template 1 is alrady connected and we create a database but using another template, like:
```sql
CREATE DATABASE myblockeddb2 -- this doesn't work'

CREATE DATABASE myblockeddb2 WITH TEMPLATE template0; -- this does work! but we're using template0 instead of template1
```

Note: template1 is the default template for new(regular) databases.

You can create your own template database to create other databases with itt. But this is however not very common.
```sql
CREATE DATABASE mysuperdupertemplate; -- a template database is actually a database!

-- connect to mysuperdupertemplate and run
CREATE TABLE mysuperdupertable ();

-- after creating the database, connect to it and you will see superdupertable

-- now if we run:
CREATE DATABASE mysuperduperdatabase WITH TEMPLATE mysuperdupertemplate;

-- now if you connect to superduperdatabase, you'll see the mysuperdupertable. Because it was used as a blueprint.
```

### Creating a database
So how do you create a database?

## 151-151 - Creating A Database
![](../img/151-151-1.png)

You can overwrite the defaults using `WITH` keyword.

Note: By default, when you create a database, the user that creates the database is the owner of that database. Though, you can specify a different 
owner.
![](../img/151-151-2.png)

UTF8 encoding which is the default is great, don't change it. With UTF8 you can store characters of different languages, you can store Arabic... .


Don't store emojis in your database please!

### Database organization
Databases often contain many tables, view, etc ... . Depending on how much you care, you may want to organize them in a logical way.

```sql
psql -u postgres postgres
CREATE DATABASE ztm; -- semi-colon is important
```

Deleting a database:
```sql
DROP DATABASE <database>
```

## 152-152 - Database Organization
### Postgres schemas
Postgres offers the concept of **schemas**. Think of it like a box in which you can organize tables, views, indexes, etc.

Schema is a construct.

By default, each DB gets a **public** schema. So when you create a schema, you have that public schema and it is the one that will be called
by default. So if you have a table in the public schema and you have a table in the sales schema that has the exact same name with the one in the
public schema, it's gonna get the public schema first.

Unless you specify a schema, the default is always assumed to be public!
```sql
SELECT * FROM employees;
-- is the same as:
SELECT * FROM public.employees;
```

![](../img/152-152-1.png)

With `\dn`, we can get all of the schemas of the connected DB and the owner of them.

Whoever creates the schema, is the owner of that schema.
![](../img/152-152-2.png)

### Reasons to use schemas
- to allow many users to use one database without interfering with each other. Why? Because if we only had a public schema and not any other one
for a DB, for example two tables with the exact same name, they would interfere with each other, because in one schema you can't have two tables
with the exact same name.
- to organize DB objects into logical groups to make them more manageable
- third-party applications(that need certain tables and ...) can be put into separate schemas, so they don't collide with the names of other objects.

So if you separate things into schemas, you can have duplicate names, you can have duplicate named objects that are not necessarily the same because
they're in a different logical group, but you're still operating on the same DB.

Each different DB is like a different universe! Because you're not gonna have the data from other DB.

### Restricted
Creating databases is a restricted action. Not everyone is allowed to do it!

## 153-153 - Roles In Postgres
### Roles
Roles are vital to any DBMS. They determine what's allowed!

A role can be a user or a group. It depends on how you setup the role.

Roles have the ability to grant membership to another role. So you can give a role to a role and when that role is given tto someone else,
they automatically have booth roles.

Roles have attributes and privileges and attributes are basically the same thing as a privilege. Like attributes, define a privilege(look at the
direction of the cross line in pic). Like you can have an attribute that says you're a super user and that can give you the privilege to do
whatever you want(the exact direction of the arrow in pic). But you can gie individual privileges to a role, that have nothing to do with
an attribute(the reverse order of arrow line is not possible). Super user is an attribute and it specifies a couple of privileges.
Individual privilege could be read access, write access, the ability to create databases.

So attributes can define privileges. Privileges can be given independently.

When a role is created, it is given certain attributes.
![](../img/153-153-1.png)

## 154-154 - Role Attributes And Creation


## 155-155 - Creating Users And Configuring Login
## 156-156 - Privileges
## 157-157 - Granting Privileges and Role Management
## 158-158 - Best Practices For Role Management
## 159-159 - Data Types & Boolean Type
## 160-160 - Storing Text
## 161-161 - Storing Numbers
## 162-162 - Storing Arrays
## 163-163 - Data Models And Naming Conventions
## 164-164 - CREATE TABLE
## 165-165 - Extra information on CREATE TABLE
File

### 165 - Create Table documentation
https://www.postgresql.org/docs/12/sql-createtable.html

## 166-166 - Column Constraints

### 166 - The importance of constraints
https://www.longdom.org/open-access/on-the-paramount-importance-of-database-constraints-2165-7866-1000e125.pdf

## 167-167 - Table Constraints
## 168-168 - Regexes
File
## 169-169 - UUID Explained
## 170-170 - Custom Data Types And Domains
## 171-171 - Creating The Tables For ZTM
## 172-172 - Extra information on ALTER TABLE

### 172 - Alter Table documentation
https://www.postgresql.org/docs/12/sql-altertable.html

## 173-173 - Adding Students And Teachers

### 173 - Postgres INSERT syntax
https://www.postgresql.org/docs/12/sql-insert.html

## 174-174 - Creating A Course
## 175-175 - Adding Feedback To A Course
## 176-176 - A Tale Of 2 Feedbacks
## 177-177 - SQL Exercises
File
## 178-178 - SQL Quiz

### 178 - SQL quiz
https://www.w3schools.com/sql/sql_quiz.asp

## 179-179 - Backups And Why They Are Important

## 180-180 - Backing Up In Postgres

### 180 - pgBackRest
https://pgbackrest.org/

### 180 - Postgres backup and restore
https://www.postgresql.org/docs/12/backup.html

## 181-181 - Restoring A Database
## 182-182 - Transactions