# 13 - Extras Redis

## 265-265 - Quick Note Upcoming Videos
Ready to learn about one of my favourite databases?

Redis is extremely useful and versatile. Although we don't use SQL with Redis, we wanted to include this database inside of
this course because it is such a common database around the industry. The upcoming videos are from my
other course - Complete Junior to Senior Web Developer Roadmap. Hope you find this bonus section useful by giving you a bigger picture
of the data industry. It should also give you a good idea of how other types of databases are used inside of a company :)

## 266-266 - What Is Caching
CPUs also have some small memory.

Classification of no-sql databases:
- key-value: redis
- document: mongodb, couchdb
- wide column: cassandra
- graph: neo4j

Even though the data is in memory, redis does take a snapshot every once in a while to save the current database contents onto the disk.
Which is great to recover from when there's unexpected shutdowns wherever you have redis running and you'll only lose the last few 
minutes of information.

Use redis when we have small pieces of data and they are not super important. We're sacrificing these for speed.

## 267-267 - Introducing Redis
We're gonna use redis for managing session(postgres is also good, but redis is fast for managing small pieces of data like session).

## 268-268 - Redis Version Updates and Playground
Heads up! In the next couple of videos I will show you how to install Redis on your computer. However, this isn't necessary for 
you to do this section of the course. If you want, you can use the online version of Redis to practice the commands I will be
teaching in the course. Simply click here for the online playground(https://try.redis.io/).

If you do want to install Redis on your computer like I do in the next video, here is a guide based on your operating system:
https://redis.io/docs/getting-started/


Also note that Redis constantly gets new updates. All the commands you will see in this section will work no matter what version of 
Redis you have as long as it's version 4 or higher.

Let's get started.

## 269-269 - Installing Redis
When it comes to DBs, most of the time you won't actually be running it from your computer. In most cases, it's gonna be on a server or another
machine that is just dedicated to that DB.

After installing, you can run make test to make sure it was installed correctly.

Then you can run the redis server by running this command in the correct directory: `src/redis-server`.

Then open up another terminal window to open the redis cli:
```shell
# First go to the redis installation directory and then run:
src/redis-cli
```

## 270-270 - Resources Installing Redis
Go ahead and install Redis onto your computer so you can follow along in the upcoming videos. Use this link to get you instructions on
installing Redis. If you don't want to install Redis on your machine, you can also use https://try.redis.io/ to follow along with the next videos online.

If you encounter any errors,

https://stackoverflow.com/questions/37103054/redis-installation-fails-when-running-make-command

https://stackoverflow.com/questions/8131008/issue-with-redis-install

## 271-271 - Redis Commands


## 272-272 - Redis Data Types
## 273-273 - Redis Hashes
## 274-274 - Redis Lists
Redis lists are implemented using linked lists rather than arrays.

So lists are useful if yu have a lot of elements and you want to add elements quickly to that list. Otherwise, you want to use sorted list in redis,
which is better when you're searching or getting elements.

## 275-275 - Redis Sets and Sorted Sets
One major difference of sets with lists is that sets do not allow repeated values.

Every member of a sorted sets is associated with a score and this score allows it to be ordered from smallest to greatest.

## 276-276 - Section Summary