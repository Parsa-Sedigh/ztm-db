# Section 10 - Database Landscape Performance and Security

## 231-231 - Brunos Request

## 232-232 - Scalability
Scalability in DBs is about the capability of the DB to handle the growing amount of data and operations. In other words, it's about
limitations on how much data we can store and also on how many operations per second it can handle.

There are 2 strategies for scalability;
- vertical scaling: making a single machine more powerful like adding more memory. This can get expensive and has limitations.
- horizontal scaling: add more machines

Relational databases are really good on a **single** machine.

The reason that no-sql became popular was because they're able to do horizontal scaling around 2000s. Since originally relational dbs had this limit or
restrictions on doing horizontal scalability. 

## 233-233 - Sharding
Sharding is to split data into different DBs(because there's so much data) and you have to implement a routing system that knows where each of the data
is located. With sharding, we can distribute the queries to all these different DBs so not all users are querying one DB and we can store more data 
as well.

## 234-234 - Replication
If the data is stored on 1 machine, if this machine breaks. We can prevent this with replication which is just replicating our data across multiple
machines(they all hold the same data) and maybe we put them in different locations around the globe.

If we insert new data into one of the DBs, eventually we need to send that new data to other DBs(other replicates) as well, so that all of them
eventually stay consistent(eventual consistency).

As long as we don't have super important data that needs to always to the second be exact, well then we can query any of the replicaates.

2 strategies for replication:
- synchronous: A client makes an update(insert or update or delete) to a DB and the DB isn't going to respond until those changes are confirmed in all of
the other db replicas and then it responds to the client. The problem with this is that those replicas are across the world and it's gonna take time.
- asynchronous: when the related db is updated, we send back the success response to user and in the background, it's gonna send the updates to other DBs.

Is inconsistent data sometimes OK? Such as number of likes on your insta photo?

For the banking info, we need to replicate synchronously.

Replication allow us to horizontally scale and it prevents us to have a single point of failure.

## 235-235 - Backups
Replication vs Backups:

Replication happens usually in real time. Usually we want to do the replication as soon as possible either with sync or async apporach.
But backups on the other hand, are sth that are done not as often because they can be expensive and take a long time.

A backup usually happens by doing a dump of data into a file(usually a SQL file).

Backup strategy

## 236-236 - Distributed vs Centralized Databases
Centralized DB means a DB that is usually kept in a single location on a given network or controlled by one company like DBs from aws. If aws
goes down your DB goes down as well.

A distributed DB is a db that is composed of multiple databases stored in multiple physical locations. But it can also mean DBs that are
controlled by different organizations. For example DBs in different locatiosn around the world, so it's distributed but it can be centrally contrlled by
amazon.

Centralized DBs usually make **data integrity** easier(data is more consistent). There's not outdated or inaccurate data because everything is
centralized, usually controlled by 1 place or in 1 physical location. But you have the downside of we lose all the data if sth happens.

## 237-237 - Database Security

## 238-238 - Exercise SQL Injection
Let's learn about one of the most common ways attackers can affect a Database: SQL Injections!

First, let's learn how it works by actually performing it on a database. Go to this link to try 
the interactive exercise: https://www.hacksplaining.com/exercises/sql-injection

Once done, you can learn about how to prevent this sort of attack here: https://www.hacksplaining.com/prevention/sql-injection

I have also included optional videos (next 2 videos) from my other course Junior to Senior Web Developer Roadmap where we discuss
best security practices for web developers. This way you get to learn how Injection attacks are prevented even before it gets to the database :)

## 239-239 - Optional All About Injections Attacks
Injection means injecting code into another piece of code.

An example of SQL injection:
```sql
INSERT INTO <table> (email) VALUES (; DROP TABLE users; --);
```
So instead of an email, we sent a piece of code.

People can put `; DROP TABLE users; --` into inputs to cause problems. 

Or inside a password field, user can send: ` or 1=1--` and DB will check if the password stored in db(actually hash of the password) is equal to
the empty string which is passed by the injected string we passed(see the injected string, it has an empty string at it's beginning before
`1=1--`). So of course the has of password stored in DB wouldn't be equal to an empty string, but we have an OR of 1=1 and it is true! and therefore
the hacker can get the user info or login without providing a correct password.

Injections can happen anytime user has to input sth, he can do injection.

Inline scripts are only run at the time when the original page is executed and run(so user can not enter a <script></script>) in an input and execute it.
But user can enter another element that can run a script(so without using the <script> tag), sth like(we assumed server sends back the user input):
<img src="/" onerror="alert(boom)" />

This is because images are injected into the DOM at the time of encountering the <img /> tag(unlike the <script>) and if the loading fails, onerror will
get called.

With this, we injected a JS code into the website.

How do we prevent this?

Instead of using `innerHTML`, use `createTextNode` and `appendChild`. With createTextNode, no matter what input is, it will convert it into
pure text and does sanitizing.

What we can do to prevent injections:
- sanitize input
- parameterize queries(prepared statements): To prevent sql injections
- knex.js or other ORMs

Sanitization is typically performed by using either a whitelist or a blacklist.

## 240-240 - Optional Storing Passwords
### Data management
bcrypt, scrypt, aragon2

pgcrypto - encrypt a few columns. Like encrypting billing address or email column of users or anything sensitive.

## 241-241 - Optional How To Store Passwords
If you want to learn more about how passwords are stored and handled on the database, back in 2016 while I was working for 
my old employer, I wrote about the best practices. Enjoy the read!

https://rangle.io/blog/how-to-store-user-passwords-and-overcome-security-threats-in-2017/

## 242-242 - Relational vs NoSQL PostgreSQL vs MongoDB Databases
## 243-243 - Future Of Relational Databases
## 244-244 - Elasticsearch
## 245-245 - S3 Object Storage
## 246-246 - Top Databases To Use