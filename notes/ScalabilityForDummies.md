## Part 1: Clones

- Public servers are all hidden behind load balancers
- load balancers evenly distribute load onto a group/cluster of application servers
- It means a user could interact with a different server everytime they make a request
- However they should always get the same result independent of server
- First golden rule of scalability: **Every server contains exactly the same codebase and does not story any user-releated data on local disc or memory**
- Sessions needs need to be stored in a centralize data store which is global to all servers
- This can be in a external db or cache like Redis, cache has better perf
- You deploy data to all the same servers by creating a machine image and turn up new servers based on that. Probably could handle this now with docker images
-  After this servers can now horizontally scale because you can deploy images to new servers and load balancers can be updated to just handle more equests

## Part 2: Database
- Updating databses requires more complexity than server images
- There are two paths you can take
    - Path 1:
        - Have a main and worker replication where workers are READ ONLY while the main is handling writes and replicating to the workers
        - <TODO> **need to look at replication of DBs as well as sharding, replication , etc**
    - Path 2:
        - You can denormalize data  and use a NoSQL type DB.
        - <TODO>  **scnearios where NOSQL is better**

## Part 3: Cache
- Databases are very slow so a cache is a way to speed things up
- With "cache" you always have in-memory data, never do file-based caching.
- A Cache is a simple key-value store and it should reside as a buffering layer between application and you data storafe.
- Service should always try to read from cache before tring to read from DB
- Caches are very fast, they hold all data in RAM
- There are 2 patterns ofcaching you data:
    - Cache Databse Queries:
        - Whenever you do a query to you DB you store the result in a cache
        - A hashed version of your query is the cache key
        - Issues with this approach are expiration and invalid data
    - Cached Objects
        - <TODO> **get a better explanation/example**
        - From my understanding it seems like caching objects are items that you construct from different data sources
        - So if you create a ProfileObject that you need to query a DB for, once that object is created you cache that?
        -
