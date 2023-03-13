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

Composite keys are keys that are formed between multiple columns to uniquely identify data, without using the foreign key.
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
In the relational model, it isn't possible to store a many to many relationship. **Technically**, you can do it, but you really don't want to.

The reason for this is you're creating more overhead(you're over-complicating your system) like:
- insert overhead
- update overhead
- delete overhead
- potential redundancy(duplicate data)

As a rule of thumb always try to resolve many to many.

Ex: A book can have many authors and an author can write many books(many to many). The way we resolve this problem is:
We put a middle entity named book_author(book author) and say:

A book(one and only one) can have many book_authors but a book_author can only belong to 1 book and an author can be the author of many
book_authors, but a book_author can only belong to 1 author.

If we were to do a many-to-many relationship, we had to have multiple columns for author_id in the book table! and since the structure of a relational
DB should stay the same(we can't change the number of columns), we should put the maximum number of authors that we can imagine for number of
columns for author_id. Wow this is so bad!

Also we should do the same in author table which is put a lot of book_id columns for each author!

This scenario is the top of the pic below:
![](../img/214-214-1.png)

The correct way is to create a new entity named author_book(intermediate table) which is the bottom of the pic.

Intermediate entities or tables are there to solve the many-to-many relationships. For example the enrollment table in student to course
relationship which is a many-to-many relationship or the lesson intermediate table between student and instructor in Driveme project.

## 215-215 - Step 5 Subject Areas
Divide entities into logical groups that are related(think schemas).

The reason for this is when you split them into separate buckets, you can hand them off to different teams.

You will not get entity relationship diagram fully fledged out from the first go. This needs time and iteration.

When we look at subject areas you can think of them like schemas but not really, because you're never going to really go one to one from
a subject area to a schema.

Subject area rules:
- all entities must belong to one subject area
- an entity can only belong to one subject area
- you can nest subject areas

## 216-216 - DRIVEME Subject Areas
![](../img/216-216-1.png)

Different interpretations lead to different subject areas. 

## 217-217 - Exercise Painting Reservations
Let's answer some questions about the system:
- What's the goal of the system?
Track painting reservations for a wealthy man 
- Who are the stakeholders?
The owner of the system, museums

Now let's follow our 5 steps of top-down to create an ERD of the system.

### Step 1: Entities
- painting
- reservation
- museum
- artist

Note: Museum makes reservations.

### Step2: Attributes

In step 2, we didn't add the primary keys and foreign keys.

Note: Address attribute in museum is not a good attribute(we'll fix it later).
![](../img/217-217-1.png)

### Step 3: Relationships:
![](../img/217-217-2.png)

- An artist can have zero or more paintings. A painting can only belong to one artist(one our constraints)
- A painting can have zero or more reservations and a reservation can belong to one or more paintings(in one reservation, you can book
one or more paintings and a painting can belong to zero or more reservations, because overtime, reservations want to rent the same
painting). **OH! We have a many to many relationship here! So we need to solve it using an intermediary entity.**
- a museum can make zero or more reservations and a reservation can only belong to one and only one museum

Step4: Many to many(fix it!)
![](../img/217-217-3.png)

### Step 5: Subject areas
- `Reservation` and `reservation_detail` are part of administration.
- painting and artist are part of inventory
- museum is part of client

## 218-218 - Exercise Movie Theatre
### Exercise: cinema
An existing ERD for this exercise is:
![](../img/218-218-1.png)

The fixed many-to-many solution:
![](../img/218-218-2.png)

We named the intermediary table `showing`.

Top-down -> we use ERD

## 219-219 - Bottom Up Design
To create a data model from specific details like existing systems, legacy systems, databases that are already in use, ... .

When we do bottom-up, we go from sth that exists and we're trying to migrate it to sth that is newer.

We need to:
1) identify the data(attributes)
2) group them(entities)

We won't use entity relationship diagrams here.

Our goal is to create a "perfect" data model without redundancy(duplicate records) and anomalies from the existing data. In other words,
the goal of the bottom-up approach is to take the existing data and create a data model that will be perfect(without redundancy and anomalies). 

## 220-220 - Anomalies
Modification anomalies are problems that arise when your database is not structured correctly. There are 3 types of anomalies:
- update anomalies
- insert anomalies
- delete anomalies

Note: All of the design techniques like ERD, top-down, bottom-up design, all of them come with the methodologies in order to create a **structure** that
is correct that tries to reduce these anomalies.

You don't want to have anomalies at all!

### Update anomalies
When we look at an update anomaly, what we're trying to do is make sure that changes apply to all of the related data.
![](../img/220-220-1.png)
Look at the above pic. Let's say that the branch Toronto changed their address. When we update the record and we do an UPDATE command,
we need to make sure that each and every one of the Toronto branch records gets updated correctly.

If we had 10 million users, when we update a simple address, we're gonna have to do a lot of updates. This is an update anomaly.

What's the problem?

We need to do a heavy update sth(address column of a customer's branch) because **the thing we wanna update isn't actually directly related to a customer**.
Of course we need to know at which branch, a customer's bank at, but having it stored directly next to the customer, is causing issue. 

Also you can see that we have redundancy here(the red rectangles).

### Delete anomalies
![](../img/220-220-2.png)

In this case which has delete anomaly, if we delete the user number 3, it's branch will also be deleted and therefore we would lose important info.

Why? Because we're storing the branch data with customer data. In other words, we have created a direct functional dependency between the customer and
the branch and we don't want that. Of course we need to know at which branch a customer bank's is. But deleting a customer should not lead to us
losing important info about branch, so by deleting a user, the branch info will also be deleted and since we don't have that branch info anywhere else,
we lose important info so it's a delete anomaly.

### Insert anomalies
![](../img/220-220-3.png)

When we add a customer, we may make a mistake about the address of the user's bank branch(the red one). Now we need to make sure the data is consistent,
how do we make sure a wrong address would be prevented?

In this case, we need to do some complex logic on server side or we need to write some code in DB on INSERT, in order to make sure if sth inserts it
wrong, that stuff doesn't get stored.

So INSERT anomalies don't let us to have data consistency.

So this dependency between branch and customer, really needs to be separated. With bottom-up design, we will look at those dependencies between data and
making them into their own entities. So in this case, we would have an entity named `branch`.

When we do DB design techniques, what we're trying to do is cause low coupling between data(we don't want dependecies between data).

### Normalised
![](../img/220-220-4.png)
Here, we split out the customer table to **only** care about customer data and it has a foreign key relationship using branch column and we have a 
separate table named branch(now branch table only cares about branch info). We don't have some redundancy now. 
branch_ids are not considered redundant info. 

Note: Before this normalization, we had so many redundant data(address of the same branch was stored multiple times in records) and we also
had anomalies.

Note: Oftentimes redundancy and anomalies go hand in hand.

We want the deps between data are as loosely coupled as possible, so that we don't have anomalies and redundancy in the same table and the way we do this
in top-down is through entity relationship diagrams and in bottom-up is through a process called normalization.

## 221-221 - Normalization
![](../img/221-221-5.png)

Do we have an existing system or a new system?

New system -> top-down design

Existing system -> bottom-up design

The end state of these designs(DB design) is to have a design(model) that has as little anomalies and redundancy as possible.

These 2 techniques, although are specifically used for their respective approach, can be used for one another. For example you can
use ERD after you do normalization in bottom-up, to validate your schema and in top-down, you can draw ERD and then you can do some normalization
if you want to, to validate your ERD. So you can use both in your design.

Using normalization is key to avoid these anomalies.

To do normalization, we must understand 2 things:
- functional dependencies
- normal forms

## 222-222 - Functional Dependencies
A functional dependency shows a relationship between attributes. It's showing a relationship between the data inside of a table. We're not talking about
relationships between tables.

Functional dependency exists when a relationship between two attributes allows you to uniquely determine the corresponding attribute's value.

In the following section, we will refer to an entity as R and attributes as A and B.

We say that A is functional dependent on B, when a value of B determines a value of A. So we have: B -> A (the arrow shows B can determine the value of A).
The arrow is how we note a functional dependency.

What does this mean?

It means that based on the value of B, you can determine the value of A. 

Let's say we have emp_no -> first_name . How do we know that this is a functional dependency?

With emp_no , we will find one and only one first_name. But the reverse of this is not true. Because we could have a lot of first_name s that are
the exact same. We can have 10 Jacks and they're all gonna correspond to a different emp_no. Therefore the emp_no is not functionally dependent on
the first_name.

General structure:

determinant -> dependent

Ex: 

student_id -> birth_date

Why this dependency is correct?

Because based on the student_id, we will find unique birth_date of an individual. But the reversed, is not correct because based on birth_date, you may
find multiple student_ids(it's not one and only one). In other words, there may be multiple students with the same birth_date. So student_id is not
functionally dependent on birth_date.

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