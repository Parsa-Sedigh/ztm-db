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
This is the default database that is created when you setup postgres(INITDB)

So the first time you initialize postgres, you can connect to the postgres database to setup your first database.

With this, we can connect to the specified <database>.
```shell
psql -U <user> <database>
```
But what if we don't give it a database?

**Postgres by default will assume a connection to a database with the same name as the user, if no database is supplied.**
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
### Role atttribue
When a role is created, it is given certain attributes.

The privileges of a role are determined in part by it's attributes. So it's like we said: When you define a role, there are 2 different part
to it: There are the attributes and there are the privileges and some of the attributes of that role can determine some of those privileges.
They can say: Hey, you can do this and this and this, because you have this attribute. But privileges can be more granular than that. You can
give specific privileges as well on top of what the attributes give you.

Certain attributes can be given to a role.
![](../img/154-154-1.png)

By giving some attributes to a role, you're also defining a privilege.

There are vast number of privileges outside of attributes as well. So when you define a role, there are certain attributes you can give it and
on top of that, you can give it privileges after that are more granular.
![](../img/154-154-2.png)

### Creating role
```sql
CREATE ROLE readonly WITH LOGIN ENCRYPTED PASSWORD 'readonly';
```
Note: Always ENCRYPT when storing a role that can log in.

You can now login with that role.

### View roles
![](../img/154-154-3.png)

### Creating role
(When you create a role) by default only the creator of the DB or superuser has access to it's objects(objects of the database). Anyone else, needs
to be given access to it's objects.
```sql
SELECT * FROM departments;
-- ERROR: permission denied for table departments 
```

### Creating a user
```sql
CREATE USER user1 WITH ENCRYPTED PASSWORD 'user1';
```
A user is the same as a role but it assumes login by default! So no need to say: `WITH LOGIN` when creating a user.

## 155-155 - Creating Users And Configuring Login
There are 2 ways of creating a role and password:
1) First connect to DB and then run sql commands like: `CREATE USER` and `CREATE ROLE`
2) without connecting to DB and by running: `createuser` which is exposed by the binaries of postgres. You can pass `--interactive` to this command too.

To alter a role and give it a password:
```sql
ALTER ROLE <role_name> WITH ENCRYPTED PASSWORD '<password>';
```

Note: ALTER ROLE command can also be used for users and not only roles.

Now if you try to connect to postgres using new users that have a specified password, without even giving the connection command, a password, you'll
still login! Even with giving the psql -U <username> -W and then enter the password, but we enter a wrong password, we will still login!

So it's not paying attention to passwords. This has to do with the configuration of postgres.

WHY?!!!

**Note:** The configuration of postgres sby default will trust all default connections.

If you open `pg_hba.conf` and `postgresql.conf`. First one specifies how you're allowed to connect(it has everything to do with the authentication) and the second one
is the general configuration of postgresql.

Now where are these files?

For this, connect to the database and run:
`show hba_file;` and `show config_file`(FOR postgresql.conf). Now it might tell you that you must be a superuser.

Now in pg_hba.conf there is a line: 
`
TYPE  DATABASE USER ADDRESS METHOD
local all      all          trust
`
So everything that has to do with local connections, by default is trusted.

`scram-sha-256` the most comprehensive one, `md5` is less secure and `password` which sends clear text and is vulnerable sniffing and all ... attacks.

We use `scram-sha-256` for external connections and for local connections, it's common to allow the superuser which is on the same machine to able to just automatically connect
to whatever DB it needs to, so you can do maintenance, but it's better to turn on password authentication because it's better to have authentication. But a lot of people prefer
on the local connection to not turn it on.

**Tip:** After changing these files, you need to restart the DB server.

Now if you set `scram-sha-256` for connections like local ones and then you try to connect to DB using password, it will fail. Because in order to be able to have a password
that is `scram-sha-256` enabled, we need to tell postgresql that it needs to use that(`scram-sha-256`) as the encryption methodology. Because right now, the
`password_encryption` (in `postgres.conf`) is md5 and we need to set it to `scram-sha-256`.

Now let's make the METHOD column to trust to login and alter our user(don't forget to restart the database server) and run sth like:
`ALTER USER <user> WITH PASSWORD <password>`

Then make the METHOD column to `scram-sha-256` and restart the server and then login.

Why we did this?

Because when we changed METHOD(authentication method) to `scram-sha-256`, we have created some users before changing this method. So those users were using the default methodology for
encryption(which was turned off, there was no encryption before).

The `password_encryption` should correspond to the METHOD column in `pg_hba.conf`. 

Then we needed to alter the created user to use the new encryption key. But now if we want to login as the postgres user(superuser), we can't. So what a lot of people tend to do is,
they make the auth method of local connection of postgres user, to trust. So now the setup would be like this:
![](../img/155-155-1.png)

## 156-156 - Privileges
Attributes can only define so many privileges(attributes define only a small subset of privileges)!

When you are not a superuser, what you can do is fairly limited depending on what default privileges are set.

### Privileges
By default, objects(tables and ...) are only available to the one who creates them.

So how do we give privileges to users that are created but aren't super users?

Privileges needed to be granted for roles and users to have access to certain data.

Until now, we've only seen how to give **attributes** to users when we create them. Well, we need to grant privileges for them:

### Granting privileges
![](../img/156-156-1.png)

## 157-157 - Granting Privileges and Role Management
To practice this lesson, let's put back authentication methods back to trust because it will be irritating since we always need to login with
password. So put all METHOD rows in `pg_hba.conf` back to `trust` only for this lesson.
```shell
createuser --interactive # and call it privilegetest in the questions that it asks and write n for all consecutive questions.

# login with postgres user on Employees database
psql -U postgres Employees
```

Then on another terminal tab, login as privilegetest user:
```shell
psql -U privilegetest Employees

\dt # to list the tables
SELECT * FROM titles;
# ERROR: permission defined for table titles
```
So only the owner and superuser have access.

So let's grant a privilege for the user. For this, switch to a user that has the privilege to grant privilege to other users and run:
```shell
GRANT SELECT ON titles TO privilegetest;
```

You can also revoke select on a table:
```sql
REVOKE SELECT ON titles FROM privilegetest;
```

EX: When you wanna grant privilege ON ALL TABLES, you have to write the schema:
```sql
GRANT ALL ON ALL TABLES IN SCHEMA public TO privilegetest;
```

```sql
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM privilegetest;
```

## 158-158 - Best Practices For Role Management
When managing roles and permissions, always go with the "principle of least privilege".

Start with no privileges at all and give privileges as needed.

Don't use admin/superuser by default.

## 159-159 - Data Types & Boolean Type
A data type is nothing more than a constraint place on a field to only allow that type of data to be filled in.

Specifying a type is important because it tells the system how it can/should handle the data in that field.

It's also what allows the DBMS to optimize it's algorithms for processing data by knowing it's type.

A boolean type can hold true or false oor null.

![](../img/159-159-1.png)
So we can fill in y or yes as true and ... .

## 160-160 - Storing Text
### Character
Postgres provides three character data types:
- char(n)
- varchar(n)
- text

### Char(n)
Fixed length with space padding.

So if we specify a column ass CHAR(10) and we pass mo to it, postgres will store **mo + 8 spaces**.
So char(n) will always fill the n characters whether or not you fill it completely.

If you go over n, it will throw an error(the same is true for `varchar`).

### varchar(n)
variable length with no padding.

If we pass mo to varchar(10), mo is the value it stores and it's not gonna pad the value with extra spaces.

So this has some space saving.

### Text
unlimited length text

Now why would you not always choose text over varchar and char?

Because sometimes you want to put that constraint of length n. You don't want to allow an infinite amount of text in a certain field.

## 161-161 - Storing Numbers
### Numeric
There are 2 types of numbers in postgres:
- integers
- floating points

### Integers
A whole number, a number that is not a fraction. There are 3 types of integers in postgres:
- smallint: Can range from -32,768 to 32,767
- int
- bigint

Bigint takes more space.

### Floating point
Numbers that can contain a decimal point. There are two types(single or double precision):

#### float4/float8
- float4: Up to 6 digits passed the decimal point(precision)
- float8: Up to 15

Note: If you pass a number with more precision(more numbers after decimal point), it will **round it up** to the related type.
For example when storing `1.12345677676` in a float4 column, it will store it as: `1.1234568`(it's rounded).

#### decimal/numeric
This type is like the bigint of floating point numbers!

Up to 131072 digits before the decimal point and up to 16383 after the decimal point.

## 162-162 - Storing Arrays
An array is nothing more than a group of elements of the same type.

An array is denoted by a bracket syntax.

Every data type in postgres has an array equivalent.

## 163-163 - Data Models And Naming Conventions
### Let's create data
We've created our DB. All that's left to do is put in data, right?

### Before we start
Hold up! What do we put in?

### Database models
Usually before creating a DB, we create a model!

A model would help you think ahead up front and not have to make as many changes as you go along. A little bit of thinking ahead, will take you away farther.

Note: A model is a design that is used to visualize what we are going to build.

An entity relationship diagram:
![](../img/163-163-1.png)

An entity relationship diagram is what we would use to create that model.

The lines between entities are called **crow's feet notation** which tell us what kind of relationship is being formed.

### Naming conventions
Table names must be singular!

Because when we read the relationship, we can read it in that way(singular).

Columns must be lowercase with underscores.

Since it's case insensitive, you can store uppercase or lowercase. Storing columns in lowercase with underscores is because you don't have to use the
quotation marks.

Columns with mixed case are acceptable. For example you can name a column: student_ID

Columns with uppercase are unacceptable.

Be consistent, write down your rules.

## 164-164 - CREATE TABLE
```sql
CREATE TABLE <name> (
    <col> TYPE [CONSTRAINT],
    table_constraint [CONSTRAINT]
) [INHERITS <existing_table>];
```

```sql
CREATE TABLE student (
    student_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    date_of_birth DATE NOT NULL
);
```
We could add a constraint oon email to make it follow a particular pattern, OR, we could offload that onto hte software that's trying to fill
sth in the DB. If it's a person filling gin data(instead of software), you may want to put some constraint on email field.

To execute the command above, first connect to the related DB:
```shell
psql -U postgres ztm
\conninfo
```

When you run the above query for creating the student table, itt will throw an error that says: `uuid_generate_v()` no function matches the given name
and argument types. You might need to add explicit type casts.

To fix this, we need to create an extension. These are extensions on top of postgres that allow it to extend it's functionality. So run:
```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```
This extension, extends the ability of UUID type.

Now run the create table for student again.

Then run `\dt` to confirm that.

To look at the structure of a table, run:
```shell
\d <name of the table>
```

## 165-165 - Extra information on CREATE TABLE
The CREATE TABLE statement is the most important command when making a database, depending on the DBMS you are using the options may vary heavily.
In Postgres there are many things you can do with this statement.

We wanted to leave a couple of notes on the creation syntax!

### Normal tables
```sql
CREATE TABLE <name> (
    <col1> TYPE [CONSTRAINT],
    table_constraint [CONSTRAINT]
) [INHERITS <existing_table>];
```
By default any time you run the `CREATE TABLE` command you will be creating an object in the public schema, unless otherwise specified.

Think of database design like drafting a blueprint for a house, and when you’re creating a table you’re drafting a blueprint for a specific room.
What you put on that blueprint will determine how that room is going to look!

In our videos you saw us using this syntax to create many different tables with varying constraints, types and keys.

For the most part creating tables is the easy part, the hardest part is drafting the blueprint upfront so that you know what steps to follow.
This is why database design and modelling is so crucial.

### Temporary tables
One thing we did not touch on during these videos is the ability to create temporary tables. They are a type of table that exist in 
a special schema, so you cannot define a schema name when declaring a temporary table.
```sql
CREATE TEMPORARY TABLE <name> (<columns>);
```

These types of tables will be dropped at the end of your session. It’s important to also note that they are only visible to the creator.

Now you may be wondering, why would I ever use these? Well if you’re writing intensive queries against a data set it might 
be beneficial to temporarily create a table based off another table.

This is because:

- Temporary tables behave just like normal ones
- Postgres will apply less “rules” (logging, transaction locking, etc.) to temporary tables so they execute more quickly
- You have full access rights to the data, if you otherwise didn’t so you can test things out.

### 165 - Create Table documentation
https://www.postgresql.org/docs/12/sql-createtable.html

## 166-166 - Column Constraints
### Constraints
Constraints are a tool to apply validation methods against data that will be inserted.

### Column constraints
A column constraint is defined as part of a column definition.

![](../img/166-166-1.png)

### 166 - The importance of constraints
https://www.longdom.org/open-access/on-the-paramount-importance-of-database-constraints-2165-7866-1000e125.pdf

## 167-167 - Table Constraints
A table constraint definition is not tied to a particular column and it can encompass more than one column.
![](../img/167-167-1.png)

![](../img/167-167-2.png)

To write a table-level constraint, at the bottom, after all the columns are defined, you need to give it a name, like: pk_<column name in table>.

Every column constraint can be written as a table constraint.

An example of column constraint:
`email TEXT CHECK (email ~* '^[A-Za-Z0-9._%-]+@[A-Za-z0-9.-]+[.][A-Za-z]+$')`

## 168-168 - Regexes
When it comes to Regular Expressions there are many avenues to take to learn and apply this skill. It in itself is a language that crosses
the barriers of each programming language and can be used to validate complex text patterns. Regex comes in multiple "flavours" or what
we would like to call "variations".

It usually finds its place in applying validations to make sure what a user is inputting matches an expectation. That expectation could be anything:
- An e-mail 
- A phone number 
- An address 
- A postal code

When it comes to data we often have unique ways of writing these things, and they vary from country to country so it is extremely important to be
able to know what inputs are expected and validate/sanitize inputs so they match your expectation.

Different languages can have slight variations on how they choose to implement Regex, but they often do not differ heavily.

At the end of the day, what we want to achieve is clean data and we have all of these mechanisms in place to help us, writing constraints is a hard job.
For learning Regex there are thousands of resources that can teach you, help you verify and supercharge your skills. We've curated a 
list here of learning resources that we found extremely valuable and helpful:

- Regex for regular folk https://refrf.shreyasminocha.me/ - A fun, illustrative and simple guide to learning regex 
- Regex Crossword https://regexcrossword.com/ - Challenging puzzles to practice your Regex-fu 
- Regex Search https://ihateregex.io/ - A tool to find quick and easy regex references 
- Regex Tester https://regex101.com/ - A Regex testing tool that supports multiple flavours

When to use column-level constraint vs table-level?

If you can define a constraint that is at the column level, make it a column level constraint, but if it encompasses multiple columns,
make it a table-level constraint. So if a constraint is related to one-column, make it a column-leve constraint, if it's related to multiple columns,
write it as a table constraint.

## 169-169 - UUID Explained
### Using UUID fields
```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```
We need to install an extension.

### Extensions
```sql
SELECT * FROM pg_available_extensions;
```
These are pieces of software that allow you to expand what postgres can do or expand how certain processes run.

### UUID
The extension allows us to generate unique identifiers for primary keys.

UUID stands for universally unique identifier.
![](../img/169-169-1.png)

But why we would use them instead of just simple numbers?

The purpose of using them is it's another point of contention. Not everyone is a fan of UUIDs.
![](../img/169-169-2.png)

You use sharding when your DB is getting extremely large.

One pro of UUID is it's easier to merge/replicate data. About the merge part: when you're merging two tables. Lets say they have 2 userId columns.
Now imagine if they had 2 users with the id as 9. That's very bad. But with UUiD, this won't happen.

Where you can and where it seems valid, try to use UUIDs as much as possible for your primary keys.

When you're starting off, use UUIDs for your primary keys as much as possible.

You can use an auto-incrementing number besides the UUID column for easy indexing and other reasons.

## 170-170 - Custom Data Types And Domains
### Custom data types
In this data model, we decided it would be good to show how to make a custom data type

Postgres doesn't have custom data type out of the box.

**Note:** Instead of making the feedback field a custom data type, we could create a separate table for it.
![](../img/170-170-1.png)
So this particular custom data type, isn't a good decision, but we'll get to that.

Postgres allows you to create custom data types to store shapes of data that are more complex.

A domain is a specific type of data that can have a CHECK. In other words, a domain is an alias for an existing type that can have constraints and
default values. But a TYPE, is an independent type, so a domain and type are different.

```sql
CREATE DOMAIN Rating SMALLINT CHECK ( VALUE > 0 AND VALUE <= 5);

CREATE TYPE Feedback AS (
    student_id UUID,
    rating Rating,
    feedback TEXT
);
```

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