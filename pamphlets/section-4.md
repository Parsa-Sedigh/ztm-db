# Section 4: 4 - Environment Setup 

## 39-39 - Why PostgreSQL
## 40-40 - Environment Tooling
Since postgres is running as a server on your machine and a server always runs on a URL, there is gonna be a URL associated with postgres and in dev,
it's gonna be localhost.

https://www.valentina-db.com/en/all-downloads/vstudio

## 41-41 - Having Trouble Registering A Serial Key For ValentinaDB
File attached

## 42-42 - SQL Tooling Alternatives
File attached

## 43-43 - Command Line 101
## 44-44 - Getting Help With The Setup
## 45-45 - WINDOWS Setup
## 46-46 - Optional Setting Up Windows For Command Line
## 47-47 - MAC Setup
On mac, the only things that are changing, are username and password. Because the app configures it's environment based on your current user.
So the user you're logged in with on a mac. So the username you use on your macbook is the username you will use when logging into postgres and the same
for password. So if you have no password, that means there will be no password.

https://postgresapp.com

## 48-48 - MAC Commandline tools
## 49-49 - LINUX Setup
You need to have a password to connect to a poostgres db. If yoou don't have one, you can create it with:
```shell
sudo -u postgres psqql poostgres
\password <user like postgres>
# Then try to connect to the DB with the password that you specified in the last step
```
As you can see when we installed postgres, by default there was no password associated with our postgres user.

## 50-50 - Importing The Databases
To connect to a postgres DB for example from valentinaDB, you need to fill in the username and password for the user that you're connecting with.
On windows and linux that would be u: postgres, p: postgres, but on mac it would be the username and password that you're logging in with.

### To import data from a script file:
Now create the DBSs that we're gonna work throughout the course. Then to fill in the DBs, in valentineDB, click on the DB
you wanna import data into it > **file > load dump**.

Do not select the "execute multiple dump statements".

Import data for all the DBs that you have created for the course.

Traditionally when you extract all of the data from a DB, it's called a database dump.

In intellij, click on a data source and then: **SQL Scripts > Run SQL Script….**

Do not delete postgres and template1 databases, because it's gonna mess up your DB.

## 51-51 - Exercise Imposter Syndrome