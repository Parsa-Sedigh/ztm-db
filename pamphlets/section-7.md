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

The order of creating objects is important in this example.
```sql
CREATE DOMAIN Rating SMALLINT CHECK ( VALUE > 0 AND VALUE <= 5);

/* We used double quotes to name the object we want to create exactly as it is(with case sensitivity). Because the default is to
create it in case-insensitive way. So if you didn't use double quotes, it twill be created as feedback and not Feedback.*/
CREATE TYPE "Feedback" AS (
    student_id UUID,
    rating Rating,
    feedback TEXT
);
```

## 171-171 - Creating The Tables For ZTM
First create the tables that don't have foreign keys to other tables.

```sql
CREATE TABLE student (
    student_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(), -- PRIMARY KEY is a constraint
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    date_of_birth DATE NOT NULL
);

/* We can't create enrollment table because it has course_id and student_id as foreign key. So let's create course table.
   
   But the course table also needs a foreign key to teacher and student. So let's create the subject table.*/
   
CREATE TABLE subject(
    subject_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    subject TEXT NOT NULL,
    description TEXT
);

CREATE TABLE teacher(
    teacher_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    date_of_birth DATE NOT NULL,
    email TEXT
);

-- We forgot the email field on student table:
ALTER TABLE student ADD COLUMN email TEXT;

CREATE TABLE course(
    course_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    "name" TEXT NOT NULL, -- name is a reserved keyword, so we need to wrap it with double quotes
    description TEXT,
    subject_id UUID REFERENCES subject(subject_id),
    teacher_id UUID REFERENCES teacher(teacher_id),
    feedback "Feedback"[] -- since we created the Feedback type as case-sensitive, we need to use double quotes when referencing it as well.
);

CREATE TABLE enrollment(
    course_id UUID REFERENCES course(course_id),
    student_id UUID REFERENCES student(student_id),
    enrollment_date DATE NOT NULL,
    CONSTRAINT pk_enrollment PRIMARY KEY (course_id, student_id)
);
```

**Tip:** When you want to apply a cse sensitivity, like creating a custom type or other DB objects, use double quotes. For example
if you want to create a custom type named Feedback with uppercase f, you have to write it's name in the command with double quotes.
So the command would be: `CREATE TYPE "Feedback" AS (...);`. If you don't do this, it will be created as feedback and not Feedback.

Also when you create sth with double quotes, you need to reference it with double quuotes as well, otherwise it won't find that object.

## 172-172 - Extra information on ALTER TABLE
ALTER TABLE is a super powerful yet humble command, yes you heard me humble! I joke, but at the same time, this one command really
has a large impact on your database.

As you saw throughout this section I used it to correct some mistakes, add some constraints, etc. but we really didn’t dive deeply
into the power that lies dormant in this command.

You may be asking yourself why, but if I were to tell you every possible way you could apply this command there would be an endless amount of
videos to supply for each use-case. Our main focus is to build your solid foundation and knowledge is power. Knowing how to apply this command is
half the battle, knowing when and where to apply it, well that’s the fun puzzle that awaits you on your SQL journey!
![](../img/172-172-1.png)

As you can see in the above image, the image ALTER command has many things that it can change, from adding and removing columns to renaming them and 
changing their definition. However, there’s so much more you can do with it!

- Change the SCHEMA of a table 
- Change the name of a table-level CONSTRAINT 
- Add and remove constraints 
- Change a column level constraint

Our main advice when using this command is to make sure you communicate changes early and set in place a plan for other
systems/software to be able to migrate, the last thing you want to happen is changing a column and suddenly all your services start breaking!

### 172 - Alter Table documentation
https://www.postgresql.org/docs/12/sql-altertable.html

## 173-173 - Adding Students And Teachers
In what order do I go ahead and create data?

That's very dependent on the user journey.

Let's create a student:
```sql
-- note: The student-id field will be auto manipulated by postgres
INSERT INTO student (
    first_name,
    last_name,
    email,
    date_of_birth
) VALUES (
   'Mo',
    'Binni',
    'mo@binni.io',
    '1992-11-13'::DATE
);

INSERT INTO teacher (
    first_name,
    last_name,
    email,
    date_of_birth
) VALUES (
             'Mo',
             'Binni',
             'mo@binni.io',
             '1992-11-13'::DATE
);
```

The first flaw of this data model is that we completely separated the concept of a teacher and a student but we don't have a
holistic user to which we could give a role, not a DB role but like the role of teacher and student and they could be both at the same time.
But currently, they're completely separate entities. But the problem is now how you would let them log in? What if they wanna bee a teacher and a 
student at the same time? Because for example, Mo the student and Mo the teacher, although they have unique ids, they're 
theoretically the same email address. SO by using the same email address, how they would log in?

We could maybe create a user table and we could have a user_id and we could have a is_student or a is_teacher flag and everyone by default that registers,
is considered a student, but being a teacher is opting in.

### 173 - Postgres INSERT syntax
https://www.postgresql.org/docs/12/sql-insert.html

## 174-174 - Creating A Course
Some commands for practicing:
```sql
INSERT INTO subject (
    subject,
    description
                     
) VALUES (
    'SQL',
    'A database management language'
);

INSERT INTO course (
    "name", -- we need these double quotes since name is a reserved word in SQL
    description

) VALUES (
    'SQL zero to mastery',
    'The #1 resource for SQL mastery'
);

/* Currently, we can create a course without subject_id and teacher_id(they can be NULL). So we need to update the constraints there.
So we need the ALTER TABLE command. But before we can even alter it, since we have some NULL values there, first we need to delete the
records that are NULL in the column we want to make it NOT NULL. So what should we do here?
   
We need migration scenario. In this case, we need to fill in the columns which we want to make NOT NULL.
   
So before a ALTER TABLE command, we need to update the records that would cause problems:*/
UPDATE course SET subject_id = '8ecb03b3-b034-4e6b-a09c-601579508f60' WHERE subject_id IS NULL;

-- now run:
ALTER TABLE course ALTER COLUMN subject_id SET NOT NULL;

-- now for teacher_id, we should make it have NOT NULL constraint:
UPDATE course SET teacher_id = 'e2d090a3-5056-4361-85e2-b41c55c34bd5' WHERE teacher_id IS NULL;
ALTER TABLE course ALTER COLUMN teacher_id SET NOT NULL;
```

## 175-175 - Adding Feedback To A Course
```sql
INSERT INTO enrollment (student_id, course_id, enrollment_date) VALUES (
    '465526bb-0a0b-4b01-803b-d6ff91ab92ed',
    'f773b5af-0f00-47c1-90b4-99a2aa919a91',
    NOW()::DATE
);

UPDATE course SET feedback = array_append(
    feedback,
    ROW(
        '8ecb03b3-b034-4e6b-a09c-601579508f60', -- student_id
        5, --score
        'Great course!' --feedback
    )::"Feedback" -- in vid, this is called feedback.
)
WHERE course_id = 'f773b5af-0f00-47c1-90b4-99a2aa919a91';
```
The drawback here is we can put whatever valid UUID(as long as we fall into proper range of a UUID) as student_id in this command!
So we can't validate the subtend_id inside a feedback.

Another drawback is we need to do an UPDATE instead of INSERT. We don't want too update the course, instead we want to insert a feedback
with an INSERT command and we want to keep it separate from course.

To fix this, we need to change the data model.

## 176-176 - A Tale Of 2 Feedbacks
We need to shape our custom Feedback data type into a table and the reason for this is when we use tables, we can follow
the relational model. There's so much you can do with tables that you can do with custom data types. Custom data types
don't allow you to do checks, foreign key references and ... .

Also doing this, will make work easier.

So the model will change to this:
![](../img/176-176-1.png)

We need to do a migration of taking the Feedback array and values of that array and putting them into the new feedback table.
```sql
CREATE TABLE feedback(
    student_id UUID NOT NULL REFERENCES student(student_id),
    course_id UUID NOT NULL REFERENCES course(course_id),
    feedback TEXT,
    rating rating,
    CONSTRAINT pk_feedback PRIMARY KEY (student_id, course_id)
);
```

Since we need to make student_id and course_id together as primary key, we need to give them NOT NULL constraint too.

Now if you run this, it's gonna throw an error and says: `ERROR: relatioon "feedback" alreeady exists` and this is because
we already have a database object named feedback(if you renamed it to Feedback, you won't get this error!).

So remove that feedback custom data type or rename it to `feedback_deprecated` and also rename the feedback column of
course table to `feedback_deprecated`.

Now you can create the feedback table.
```sql
INSERT INTO feedback(
    student_id, course_id, feedback, rating
) VALUES (
    '465526bb-0a0b-4b01-803b-d6ff91ab92ed',
    'f773b5af-0f00-47c1-90b4-99a2aa919a91',
    'This was great',
    4
);
```

## 177-177 - SQL Exercises
Alllllright! We've seen so many SQL commands and it's time to put all of that knowledge to the test the
following link will take you to a series of exercises that you can power through!

Some of these exercises may be familiar as we've solved a couple early on, but now it's time to put all your knowledge to
the test!

https://www.w3schools.com/sql/sql_exercises.asp

## 178-178 - SQL Quiz
Take the following quiz to test out all the knowledge you've gained so far!

### 178 - SQL quiz
https://www.w3schools.com/sql/sql_quiz.asp

## 179-179 - Backups And Why They Are Important
Some of the way that you can go about formulating a strategy around your backups.

### Have a plan
3 things you need when you're thinking about backups
1) backup plan
2) disaster recovery plan: This plan tells you exactly what to do when a DB went down.
3) test your plans

### What can go wrong?
When everything is on premise, we have our own hardware and servers, we need to think on our backup strategy.

Even when you're in the cloud(AWS), you need to think about backup strategy, you can't rely on these cloud defaults 100%.

- hardware failures: We can have fault tolerance at hardware level.
- viruses
- power outages
- hackers
- human error

### How do I make a backup plan?
1. Determine what needs to be backed up. Always do a full backup of your DB first.
#### What to backup
First, you need to do a full backup and then, you can do other types of backups.

1. full backup  
2. incremental: When you do an incremental backup, you're backing up everything that changed since the last incremental backup(since the prev backup)
3. differential: This is always everything that changed **since the last full backup**. So the incremental backup is always faster than differential backup.
Because the differential backup is always going to take a backup of everything that changed since the first full backup. You may want to
take full backups more frequently to keep the differential backups going fast.
4. transaction log

![](../img/179-179-1.png)

You can do a full backup at the start of month and a differential at the end of the month and if there is a disaster, you only hae to run one full backup and one
differential. But it depends on DB.

These backups have different levels of freshness. A transaction log backup is the freshest.

2. What's the appropriate way to back up?
Are we going gto back up the operating system or are we going to back up at the hardware level or are we just going gto back up the DB itself?

3. Decide how frequently you're going to back up

![](../img/179-179-2.png)

Transaction log backup should be a lot because if a disaster strikes in that minute, you wanna be able to backup to the spot by the minute where the last
transaction happened and hopefully the rest of the system catches that failure and stop the transactions from happening, so that you don't lose a lot of data.
Transaction logs backup can happen for example every 15 minutes.

4. Decide where you're going to store them(where are the backup servers?)
5. Have a retention policy for backup(how long we're gonna keep this backup?). For example if you're in medical or financial space, you may have to
keep records for up to 10 years!

## 180-180 - Backing Up In Postgres
We can create dump files in different ways:

We can export them as scripts that can be easily run against a different RDBMS and is readable or we could create them as backup files which aren't SQL
commands generally speaking but postgres can understand to back up and restore more quickly.

**pgBackRest** is a tool for backup and restore.

In valentina studio, we can right click on DB > create dump.

SQL, tar and custom options are the most popular dump types.

The structure only option in valentina studio when creating a dump, is gonna take only the tables, relationship and their constraints.

Structure and records both the table, constraints and ... and data.

If you have a an extremely large DB, you don't want to store it all in one text file. There are more efficient backups. That's why we have
hardware backups which is cloning hard drives.

### 180 - pgBackRest
https://pgbackrest.org/

### 180 - Postgres backup and restore
https://www.postgresql.org/docs/12/backup.html

## 181-181 - Restoring A Database
We want to restore our backup(like a SQL dump file).

The way you decide to create a backup, influences the way that you're able to restore that dump.

You can use **Load dump** option in valentina studio. You have 2 options there: 
1. Database: Restore to a DB(existing)
2. Connection

Deselect `execute multiple dump statements` option.

In vid, since we decided to generate the SQL script dump file with a `CREATE DATABASE` command, valentina studio had a problem on the restoring the dump.

We could fix the script ourselves, but let's just load the dump as it is.

Normally you would load the dump against the DB and it would insert everything into it. But now we want to run a script because that's' what we 
generated as dump, is a script.
```shell
psql -d postgres
\conninfo
\i <path to the sql dump file>
```

GUIs although easy to generate backups, can generally make it more difficult to restore. For example in vid, we needed to restore the backup through CLI
and we had problems with it when using valentina studio. Because the script was built by **psql** to be run in command line, not to be run in valentina studio. 
So even though the valentina studio can't process the script, it generated the correct script(sql dump) with the psql command.

## 182-182 - Transactions
### Transactions
A DB is a shared resource, so many users may access it concurrently.

A transaction is a unit of instructions(query for instance) that is going to run in isolation and not affect the state of the DB until it's done.

Een when you're doing a SELECT, it's a read transaction. Yeah, that won't alter the state of DB but nevertheless, wee consider it a transaction because
you're transacting with the DB, you're giving it a instruction.

The concept of transaction, is to keep things consistent. 

So how do we keep things consistent?

Whatever happens during the transaction, stays in that transaction until you either decide that you want to commit or roll it back.

The DBMS has a mechanism in place to manage transactions.

### Lifecycle of a transaction
![](../img/182-182-1.png)
For the SELECT statements, it will never get into a failed state, because it's a read-only transaction.

When we're in a partially committed state, its not persisted to the DB just yet, to prove this, if you open up another terminal window and connect to the
DB(another connection session) that has a transaction in partially committed state and look at the data, you see the old state.

Also when a transaction is happening(like if it's in partially state), since we know when a transaction is happening, it will lock the resources that it's
using, now, if you in another connection session, start another transaction and use those locked resources in that new session, the command **will hang**!

Like:
```shell
BEGIN;
DELETE FROM employees WHERE emp_no BETWEEN 10000 AND 10005;
# We haven't commit this transaction yet
```
Then on another session:
```shell
BEGIN;
DELETE FROM employees WHERE emp_no BETWEEN 10000 AND 10005;
# This will hang here! NOthing will happen because the resource is already locked
```

You can write `COMMIT;` or `END;` to commit a transaction.

To maintain the integrity of a DB, all transactions must obey **ACID** properties.

### Atomicity
Either execute entirely(all of the transaction) or not at all

### Consistency
Each transaction should leave the database in a consistent state(commit or rollback)

if the transaction succeeds in whole, it's going to commit or if anything in the transaction fails(any of the quires in a transaction),
it's going to rollback.

### Isolation
Transaction should be executed in isolation from other transactions.

Also the order which the transactions occur, is important.

Why?

Because transaction A may affect how transaction B will execute, because transaction B may trying to access the same resources and so if they';re
trying to access the same resources, if transaction A goes first and it changes sth, it may cause sth in transaction B to fail, so transaction B is gonna 
roll back probably.

Whatever happens in transaction A, isn't known to transaction B until time of commit or rollback.

### Durability
After completion of a transaction, the changes in the database should persist.

So when your transaction commits, it's in the DB.
