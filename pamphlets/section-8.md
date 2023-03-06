# Section 8 - Solving The Mystery

## 183-183 - Clue 1 and 2


## 184-184 - Getting ready to solve the Mystery
It's time to setup your environment for the great theft of Keiko Corp! Given we're SQL masters at this point it's time to put on
our Sherlock Holmes hat and try to sniff out who did it!

We have two solid clues so far:

- It was an inside job
- It was done on 2020-06-23

With the assumption that it could be an inside job, Keiko Corp has given us full access to their Employee database and their 
number 1 product: Movr the ride-sharing platform that has taken the globe by storm! Bruno's hoping that by sifting through all of 
the data we may uncover some connection with the breach!

For this part of the course you'll need 2 databases:
1. Movr_Employees: This database contains all relevant information about the Employees of Keiko Corp.
Note: this database is slightly different than the Employees database you've been using throughout the course. You will see
me reference the "Employees" database in the solution videos, however, this is an altered version of the data for the purpose of solving the mystery!
2. Movr: A ride-sharing product that Keiko offers to consumers.

You can find the files for the Movr and Movr_Employees database in the resources section.

To restore the Movr backup make sure to do the following steps in order!

1. Create a database named Movr 
2. Restore the schema.sql first to the Movr database 
3. Restore the data.sql next

To restore the Movr_Employees backup make sure to do the following steps in order!

1. Create a database named Movr_Employees 
2. Restore the movr_employees.sql

## 185-185 - Clue 3

## 186-186 - Exercise Solving The First Clues
It sounds crazy, but Bruno's getting a bit paranoid and wants to cover every possible avenue to figure out who did this.

Keiko employees and their family travel for free with the Movr platform and since Keiko is located in the heart of Downtown New York,
he's thinking they may have used the platform to get around.

With what we know now, we should be able to deduce some information from the Movr database. Let's try to figure out what vehicles were at 
the location of Keiko Corp, and let's not forget our very first clue!

- Date of incident: 2020-06-23
- Keiko Corp Latitude: -74.997 to -74.9968
- Keiko Corp Longitude: 40.5 to 40.6

1. With this information we should be able to figure out which Movr rides happened that day around the office!
Try to sift through the database and figure out what table we need to query and what our query would be!
2. Once you figure out what rides occurred around the office that day, Bruno asks you to find the vehicle and owner info linked to those rides.
Make sure you don't have duplicates! (see image below for expected columns)

![](../img/186-186-1.png)

Now that we know which vehicles were linked to those rides we use their current location to go and interrogate them for more information!

## 187-187 - Solution Solving The First Clues
```sql
CREATE VIEW suspected_rides AS
SELECT * FROM vehicle_location_histories AS vlh
WHERE
    city = 'new york' AND
    long BETWEEN 40.5 AND 40.6 AND
    lat BETWEEN -74.997 AND -74.9968 AND
    vlh.timestamp::date = '2020-06-23'::date
ORDER BY long;

-- narrow down the suspected_rides to the drivers. So let's query that view:
SELECT DISTINCT r.vehicle_id
FROM suspected_rides AS sr
JOIN rides AS r ON r.id = sr.ride_id;

-- So now we have the suspected vehicles(should give 89 rows)

-- With this query, we will find 89 drivers that were around the keiko corp that exact day:
SELECT DISTINCT r.vehicle_id, u.name AS "owner name", u.address, v.status, v.current_location
FROM suspected_rides AS sr
         JOIN rides AS r ON r.id = sr.ride_id
         JOIN vehicles AS v ON v.id = r.vehicle_id
         JOIN users AS u ON u.id = v.owner_id;
```
## 188-188 - Clue 4
It's not drivers. The suspect is one of the riders. Find which riders were in the area during that time.



## 189-189 - Exercise Clue 4
DARN! All the work and the interrogation of the drivers has come up flat! Interestingly enough, we should have been looking at 
the riders all along. It was staring us right in the face.

So with our current setup, we go ahead and filter out all of the unique riders that were on those suspected rides on that horrible day of the theft.

Make sure to rename the column to "rider name"

![](../img/189-189-1.png)

## 190-190 - Solution Clue 4
```sql
/* People might had multiple trips that day, we don't want duplicate records, so use DISTINCT keyword. */
SELECT DISTINCT r.vehicle_id, u.name AS "rider name", u.address FROM suspected_rides AS sr
    JOIN rides AS r ON r.id = sr.ride_id
    JOIN users AS u ON u.id = r.rider_id;

-- now we've found the suspected riders
```

## 191-191 - Clue 5 and 6
It's not the riders. It was an inside job. It was 2 people that collaborated together.

## 192-192 - Exercise Clue 5 and 6
Oh boy, this is turning into a doozy of a mystery. Bruno was right all along!

For this part of the mystery, we're going to have to do something special. We're going to have to cross-reference data 
between 2 separate databases. That's crazy! How do we do that?

Not to worry, it's simpler than it sounds. Just like when we generated those UUID's we need to add a new extension to our database and 
so we need to install it!
```sql
create extension dblink; -- run this to run queries across databases
```

This will install a special utility called dblink(https://www.postgresql.org/docs/9.3/dblink.html) that we can then use to query and filter across databases.

Here's an example on how to use dblink

```sql
SELECT *
FROM dblink('host=localhost user=postgres password=postgres dbname=Employees', 'SELECT <column> FROM employees;') AS t1(<column> NAME) 
```

- In the above query, you must be aware that the AS clause in the from statement is extremely important to be able to correctly refer
to and work with the selected data
- Also note that it is a bit different than the normal syntax we've seen as with dblink. You must return an alias that maps the columns returned and the
NAME identifier you see is doing just that
- Try to write a query with the above that returns the first and last name, but make sure you're running this query from the Movr database

Now that we're dblink masters, let's get on to the more interesting query.

1) We need to create a view that solely contains the first and last name separated of the suspected riders, as their name field contains both first and 
last name combined. Use the SQL function [split_part](https://www.postgresql.org/docs/9.1/functions-string.html) to split the rider's name into
two segments (first_name, last_name)!
2) Then we need to write a query that connects to the Employee database with dblink and cross-reference the first and last names of 
the riders against the names of the Employees

With the information from this query, we should be able to narrow down who did it!

## 193-193 - Solution Clue 5 and 6
```sql
/* Our goal is to cross-reference the first_name and last_name of suspect_rider_names with first and last name of an employee.
   
   In Movr_employees DB, the first name and last name are split. But in Movr DB, we have a column named name holding both 
   first name and last name(it's poorly normalized), so we need tosplit it.
   
   We used DISTINCT here because a user may have taken a ride multiple times.*/
CREATE VIEW suspect_rider_names AS
    SELECT DISTINCT 
        split_part(u.name, ' ', 1) AS "first_name",
        split_part(u.name, ' ', 2) AS "last_name"
    FROM suspected_rides AS vlh
    JOIN rides AS r ON r.id = vlh.ride_id
    JOIN users AS u ON u.id = r.rider_id;

/* assuming you have created the dblink extension and you have it available on Movr DB, what we're doing here, is we're linking to an external DB on the
   same server or on a different server(because we can give it a host, so it could be local or external).
   
   The second param of db_link is the query you want to execute on the external(or local) db.
   
   When you do a dblink(), it expects you to create a column definition list for the returning data, that's why we do an alias.
   
   We want to represent the rsults based on concatenated first_name and last_name but join with last_name columns, so we need to use
   CONCAT() function.*/
SELECT 
    CONCAT(t1.first_name, ' ', t1.last_name) AS "employee",
    CONCAT(u.first_name, ' ', u.last_name) AS "rider"
FROM dblink('host=localhost user=postgres password=postgres dbname=Movr_employees', 'SELECT first_name, last_name FROM employees;') AS t1(first_name NAME, last_name NAME)
JOIN suspect_rider_names AS u ON u.last_name = t1.last_name
ORDER BY "rider";

/* The result is 11 results that show an employee of our company related to the rider on their ride. */
```
## 194-194 - Solving The Mystery