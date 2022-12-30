# Section 3: 3 - Databases SQL Fundamentals

## 14-14 - SQL Playground
db-fiddle.com

## 15-15 - What Is SQL
## 16-16 - What Is A Query
query == SQL statement

SQL is a declarative language.

![](../img/16-16-1.png)

## 17-17 - Exercise Setting Up Your First Database
## 18-18 - Imperative vs Declarative
A declarative language is a lang where we say: "What will happen"(but we have no idea how it will happen).
Declarative is powerful because of it's simplicity.

Imperative: How it will happen? It means line by line instructions. Imperative is more flexible and more powerful, but it becomes more complicated.

Java is imperative, but python can be both declarative and imperative.

## 19-19 - History of SQL
SQL is a language that is standardized.

## 20-20 - Optional History of SQL Deep Dive
File attached
## 21-21 - Exercises The Select Statement
File attached
## 22-22 - SQL Standards
SQL is a standardized lang

## 23-23 - What Is A Database Revisited
File processing systems:

Flaws:

You were saving data on individual files and there was no correlation between the files which means there were no relationship between the data.
The software was so customized to the system, you even needed to kow how your data was being stored, what hard drives you were using, what software
was running on the server and ... , you needed to know all of that in order to write the software(there is a lot overhead).

All of the systems did not have common ground meaning they were all built custom. So the user needed to know each and every particular system
in order to work efficiently.

For a school system, we would have a student system, exams system, library system and these systems are completely independent from each other:

![](../img/23-23-1.png)

There is no common model and model means: Hey, my data is gonna follow this structure. But in a file processing system, the structure of data were different
in systems. This means if a student were to loan a book to study for a particular exam, the library would cop the student data and then store it and
exam would copy the  student data when the student took the exam, so we have redundant data and the data is isolated and none of the systems could talk to each other.
These are huge limitation.

## 24-24 - Database Oriented Approach
In database approach, we migrated to a singular system instead of multiple.

![](../img/24-24-1.png)

What do we mean by database approach?

There is this software that is utilized to manage DB and it's called a database management software. It's sole purpose is to put constraints in place.
It's going to manage your data. That's itt's only task and the way it does this, is by following a model. So different DB softwares follow different
models. The DB management system is gonna enforce a structure of data that each and every one of the systems(like library, exams and students) needs to
follow and by enforcing that structure, now no one needs to care about where the data is stored. This isn't important anymore because all of these systems
are following the same principles and there is a layer of software in front of whoever's talking to it that is doing this management. That means you don't
need software developers anymore to create custom solutions, you don't need all of those migrations and gluing together systems that you previously needed
to make systems talk to each other. What you have now is one piece of software that promised you iif you follow this model oof the way to
save your data it will do all of the management for you and that is what database management software did.

**TODO: Write down all of the important stuff!**

## 25-25 - Exercise SQL Starter Quiz
File attached

## 26-26 - Database Models
A DB model is nothing moore than a way to organize and store your data, but it also does so much more than that.

A lot of these models are designed with rules and concepts in place that allow you to create a view of the data that you're going to store.

Every child can only have 1 parent.

![](../img/26-26-1.png)

## 27-27 - Hierarchical And Networking Model
Hierarchical model is fairly old. It isn't an efficient model to store data.

![](../img/27-27-1.png)
        
Every child(node) only can have a single root. It means there is no ability to say: Mo and andrei both co-authored book 1. XML is like this model.

The hierarchical model supports 1-to-many relationships.

![](../img/27-27-2.png)
Network model allowed many-to-many relationships. Children could have multiple parents.

In network model and hierarchical model, we were storing data in XML-like format(parent-child structure)

## 28-28 - Relational Model
The first model with the formalized set of rules.

In a relational model, we're storing all of our data separately. We don't have parent-child structure anymore, we have a table structure.
So now instead of having an author parent, each and every author is an individual piece of data with a unique identifier and each and every book is an
individual piece ofo datta with a unique identifier. Now we know that they're completely separate, butt how do owe link them together?
In the relational model, we have concepts on how to draw relationships between data.

One way of doing it is to create a third table(linking identifiers in a separate table).

The logic of how the relationships are linked, are managed by the database management software.

![](../img/28-28-1.png)



## 29-29 - 12 Rules of Codd

## 30-29 - DBMS Revisited
DBMS can do CRUD.

TODO: Write down the stuff!

## 30-30 - Relational Model Revisited
Each and every DBMS and DB, follows a specific way of saving data that we call a model and each and every DBMS enforces the rules of these models.

![](../img/30-30-1.png)

## 31-31 - Tables

## 32-32 - Columns
The degree of the relation: is the collection of all the columns of a table.
    
When we talk about what a column can store? We call that the domain or the constraint. For example in dob column,we can only store dates(your constraining
the data - we're specific about the domain of the column - domain is the constraint of the data, iit specifies what can go to a column).

![](../img/31-31-1.png)

Another way ofo talking about columns is: My table has these attributes with these constraints.

Column == attribute

degree of user table in pic: id, firstName, lastName, se, dob

You can say: my column has _datetime_ domain. Each and every column may or may not enforce a specific type of data to be saved in it. We call these
constraints or the domain of the attribute(attribute domains).

## 33-33 - Rows
tuple = row = single record of data

All of the tuples or rows(all of the data that iis in the table), is called **cardinality**.
All of the columns is called degree.

## 34-34 - Primary And Foreign Keys
## 35-35 - OLTP vs OLAP
## 36-36 - Exercise OLTP vs OLAP
## 37-37 - Exercise Relational Model Quiz
## 38-38 - Endorsements On LinkedIN