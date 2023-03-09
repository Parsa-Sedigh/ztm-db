# Section 9 - Database Design

## 195-195 - System Design And SDLC
### Database system design
![](../img/195-195-1.png)

The goal of this process(SDLC) is to design robust(low coupling and high cohesion) systems.

The process of SDLC can be implemented in different ways: agile, waterfall, v-model, ... .

Scrum is a methodology of agile.

If we were in the healthcare industry, and we're building a software, you don't want them to constantly ship that software with updates because
you don't want bugs in that software(like a heart monitor), you want that to be well-tested and ... . So that's' why waterfall exists. It's a 
regimented process for industries that are heavily regulated.

So software development lifecycle of regulated industries are much slower than an agile lifecycle, because they follow a regimented pattern.

## 196-196 - SDLC Phases
### SDLC
Different models, cycle through the steps at different paces.

### SDLC: Phase 1 (requirements analysis)
This entire phase is about getting the information on what needs to be done(scope).

### SDLC: Phase 2 (system analysis)
Taking in the requirments and analyzing if it can be done on time and on budget

### SDLC: Phase 3 (system design)
Designing the system architecture for all related components: databases, apps, etc(glue together how the pieces will work).

### SDLC: Phase 4 (system implementation)
Building the software

There are more phases that can be added: testing, maintenance, ... .

Testing should be a continuous thing in phase 4, it shouldn't be a separate phase that happens after phase 4. This is what agile preaches as well.
You should keep the implementation and the testing as close as possible.

So far we've been playing around in phase 4, now **we'll be taking a look at how you can do phase 3 for databases**.

Let's look what system design looks like for databases.

Phase 1 and 2 are closer to business.

## 197-197 - System Design Deep Dive
### From chaos to structure
System design is all about creating structure that cab be understood and communicated.

### Database design
In any system, there is data!

There are different techniques to design databases and there are 2 popular ways of designing:
- top-down
- bottom-up

## 198-198 - TopDown vs BottomUp
### top-down
You start from 0. There is no existing system and data.

Top-down is the optimal choice when creating a new database.

All requirements are gathered up-front.

### Bottom-up
There's an existing system or specific data in place.

### What to use?
Often you'll end up using a bit of both.

## 199-199 - DRIVEME Academy
In top-down design, you create the universe based on the requirements.

Usually the core requirements are a well-thought out document with sections of functionality.

For this example, we've gone ahead and summarized the requirements. So there's no phase 1 and phase 2 for us, we go staright into phase 3.

1. There's a vehicle inventory for students to rent
2. There are employees at every branch
3. There is maintenance for the vehicles. So we want to make sure that maintenance schedule doesn't coincide with someone's lesson
4. There is an optional exam at the end of your lessons
5. You can only take the exam twice, fail twice and you must take more lessons

We need to model the DB now.

## 200-200 - Top Down Design
The goal of top-down design is to create a data model based on requirements.

### Requirements
What are we looking for when we talk about requirements?
- high-level requirements
- user interviews
- data collection
- deep understanding

### Method
One of the key methods to top-down design(one of the methods we use in top-down) is ER-modelling.

## 201-201 - ER-Model/ERD
ERD = entity relationship diagram

It's a diagram that functions as a way to structure high-level requirements.

An ERD:
![](../img/201-201-1.png)

## 202-202 - Step 1 Determining Entities
Step 1: Entities
Determine what entities are in the system.

### What is an entity
- a person/place or thing
- has a singular name
- has an identifier
- should contain more than one instance of data

## 203-203 - Tooling For Diagramming
UML is the language that we use when we create ERD.

UMLet-lucidchart are some of the tools.

## 204-204 - DRIVEME Academy Entities
In our example, is maintenance an entity or a data that we hold?

When we think if a maintainable, we mean: date of maintenance, what kind of maintenance, who did it? Where did it happen? 

So we're edging towards making it an entity, because we're storing multiple facts about maintenance. Since we want maintenance to be granular, we don't
want to just store a date of maintenance so that we can make it a column in vehicle table.

So it's an entity.

About exam:

Student can take multiple exams, so there are multiple instances of an exam and it can have more info. So an exam is an entity.

The 5th requirement that was mentioned before, is a constraint.

---

You could say that the school entity has lessons, has vehicles, has exams, you could say that school can be linked to everything, but we 
don't do this. We just linked it to instructor and student. Why?

Because there are orders of relationships. Since school is linked to student and instructor, it can derive through the relationships, all of the
different information. There is nothing that school is not connected to, by being connected to instructor and student. By being connected to 
student and instructor, you can derive all of the chain downward.

Now **if** a school were to offer **specific lessons**, you could link a lesson to a school. But if all the schools offer the exact same lesson packages,
then it's not important to know which schools offer which lessons, because by knowing the instructors, you can derive this information.
![](../img/204-204-1.png)

Now in the step 2, we need to look at the entities and determining what they need to store and we will again look at the relationships and
we'll re-evaluate them to structure them in a way that can be made into a DB.

## 205-205 - Step 2 Attributes
Give entities the information they will store(also known as the columns).

What are attributes:
- must be a property of the entity(have to belong to that entity)
- must be atomic(can only hold a single value): For example an address. It's not good to encompass all the street, postal code and ... in one field
named address(so it's not atomic)
- single/multivalued(phone number): A field can contain a single phone number or multiple phone numbers, for example array values. So single/multi valued
is not the same as being atomic. When we talk about being atomic, it's about narrowing it down to the most small amount of information that needs to be
stored for an individual field
- keys

## 206-206 - Relational Model Extended
Relational model have things like:

relation schema, attributes, degree, cardinality, tuple, column, relation key, domain, tables, relation instance



## 207-207 - Relational Schema And Instance
### Relation schema
Relation schema refers to the table schema. It's a representation of the data that's going to go into the table
![](../img/207-207-1.png)

Relation schema from high level, is a table and when we talk about the relation schema for for example the user table, we say: In the user table,
we're going to store id, first_name and ... . That is what we call the relation schema.
![](../img/207-207-2.png)

Relation schema refers to the name of the table and it's columns.

### Relation instance
Relation instance is the set of data that relates to the relation schema. So it's referring to all of the tuples or rows or data inside of that table.
That is what we call the relation instance.

![](../img/207-207-3.png)

## 208-208 - Super Key and Candidate Key
![](../img/208-208-1.png)

The way that we structure our relationships is by uniquely identifying each and every row and the relation key's purpose is exactly this. It's
going to uniquely identify each and every row in a dataset and this is necessary in the relational model because we're structuring our data
in an unsorted and unordered matter.

If you want to form a relationship, then you're gonna need a relation key. If you're not going to form relationships, you can just
store your data unsorted, flat with duplicates, it wouldn't matter but the moment that you want to form a relationship, you're gonna
have to uniquely identify your row.

Relation key has 2 jobs:
1) Uniquely identify each row
2) uniquely identify the relationship 

So relation key is like a glue. It glues together the relationship. A relation key is sth that will uniquely identify the row and uniquely 
identify the relationship.

What are types of relation keys?
- super key
- candidate key

### Super key
![](../img/208-208-2.png)
A super key is any combination of attributes that could uniquely identify a row.

In the pic above, id, first_name and last_name is a super key and ... . We know email is unique because we don't allow duplicate ones.
So we could say that the combination of email and first_name or email and id and ... are super keys.

### Candidate key
Candidate key is the **minimal** amount of attributes that are needed to uniquely identify the row.
![](../img/208-208-3.png)

It can be a candidate key, email can be, but id, first_name, last_name are not.

## 209-209 - Primary Key and Foreign Key
Once we have the key that uniquely identifies the row, we're gonna call that our primary key.

Primary key is that relation key, it's that key that's gonna uniquely identify the relationship.

We select one column instead of multiple column as our primary key, because it simplifies our design.

![](../img/209-209-1.png)

When we want to make a relationship, we're gonna add a column to our table as foreign key which links to the primary key of another table
and that's what we call a foreign key relationship and by doing this, now the data that is filled in, automatically has a constraint and
that constraint is that we can only fill in values that match a value that exists in the primary key of another table that we're defining the
relationship with.

## 210-210 - Compound Composite And Surrogate Key
![](../img/210-210-1.png)

The super key encompasses all the different types of keys.

When you use a key that is creating a uniqueness to a row and it's using a foreign key, we call that a compound key.

Composit keys are keys that are formed between multiple columns to uniquely identify data, without using the foreign key.
![](../img/210-210-2.png)

### Surrogate key
When we choose a primary key(which will uniquely identify a row) but has nothing to do with the individual's data, we call that a surrogate key.

## 211-211 - DRIVEME Attributes
Instead of address which is not atomic enough, use these: street_name, street_nr, postal_code, state, city.

## 212-212 - Step 3 Relationships
Determine the relationship between the entities.

### What is a relationship?
Links 2 entities together.
- 1 to 1: One entity belongs to one of another entity. 
- 1 to many
- many to many

We connect the entities with a line that tells us the relationship.

![](../img/212-212-1.png)

First line means the line that are connected directly to the entity(left end)

Second line is like those circles. The constraints in the order of pics:

zero or more(sth can have multiple values)            one and only one(sth needs to have a value and only one value)

one or more(sth needs to have multiple values)        zero or one(sth can have a value but doesn't need to)

Note: Second line is optional.

As you can see, the first one is constraint and the second one is the relationship, for example in the first one(top left), the constraint is
zero and the relationship is more(so first we read the constraint and then the relationship).

Crowsfeet depict the type of relationship that can occur.

![](../img/212-212-2.png)

## 213-213 - DRIVEME Relationships
Taking 2 exams is a loos business constraint, so we don't implement it in DB level, instead we implement it in code level of our backend(it's a 
loosely coupled constraint).

![](../img/213-213-1.png)

We don't have any many-to-many relationship in this system!

No many to manys is great.

## 214-214 - Step 4 Solving Many To Many

## 215-215 - Step 5 Subject Areas
## 216-216 - DRIVEME Subject Areas
## 217-217 - Exercise Painting Reservations
## 218-218 - Exercise Movie Theatre
## 219-219 - Bottom Up Design
## 220-220 - Anomalies
## 221-221 - Normalization
## 222-222 - Functional Dependencies
## 223-223 - Functional Dependencies 2
## 224-224 - The Normal Forms

### 224 - Simple guide to normalization
http://www.bkent.net/Doc/simple5.htm

### 224 - Useful summary of normalization
https://www.cs.uct.ac.za/mit_notes/database/htmls/chp09.html

## 225-225 - Going from 0NF to 1NF
## 226-226 - Going from 1NF to 2NF
## 227-227 - Going from 2NF to 3NF
## 228-228 - BoyceCodd Normal Form
## 229-229 - Why 4NF And 5NF Are Not Useful
### 229 - 4th normal form
https://www.studytonight.com/dbms/fourth-normal-form.php
### 229 - 5th normal form
https://www.studytonight.com/dbms/fifth-normal-form.php
## 230-230 - Exercise Database Design Quiz