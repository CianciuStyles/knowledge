# Databases

## Notes

### A Dive Into Storage Engines

* The storage engine is responsible for storing, retrieving and managing data in memory and on disk. A DBMS will often let you pick which storage engine you want to use.
* The two most popular types of database storage engines are
  * Log Structured (like Log Structured Merge Trees): treat the database as an append-only log file where data is sequentially added to the end of the log;
  * Page Oriented (like B-Trees): breaks the database down into _fixed-size_ _pages_ (traditionally 4 KB in size) and they read/write one page at a time.

#### **Log Structured Storage Engines**

* Let's suppose we have a key-value store supporting two functions - db\_set(key, value) and db\_get(key).
* Anytime you call **db\_set**, the (key, value) pair will be appended to the bottom of the log, _regardless_ of whether the key already exists in the log. Hence, the **db**_**\_**_**set** function runs in O(1) time since appending sequentially to a file is very efficient.
* Anytime you call **db\_get**, you look through all the (key, value) pairs in the log and return the _last_ value for any given key. Hence, **db\_get** runs in O(n) time, since it has to look through the entire log - but we can make it faster by adding a _database index_ (at the cost of making writes slower, as now for every write both the log and the index must be updated).
* An example of index that can be added in the database is a hash index - an in-memory hash table where keys are the keys added to the log and values are the offset in the log where the key can be found. This works well until all the (key, offset) pairs _fit into main memory_.

### How Distributed Databases Work

* Replication is where you keep a copy of your data on multiple different machines. These machines are connected via a network, so they’re all accessible to your backend server(s).
* There are several reasons why you’d want to replicate your data across several computers:
  * **Reduce latency** - Users in India can send requests to the nodes located in Delhi while users in America can send requests to the nodes located in New York.
  * **Increase Availability** - If one of the nodes goes down for some reason you’ll have another node that can take over and respond to requests for data.
  * **Increase Read Throughput** - Several nodes have the ability to respond to read queries instead of just having 1 machine doing all the work for read requests. Many workloads are _read-scaling_ (consist of mostly reads and a small percentage of writes) so increasing read throughput is extremely helpful.
* When you get a write request that modifies your database, how do you make sure that all the replicas reflect this write request? There are 3 popular strategies for writing changes to all your replicas:
  * **Single Leader Replication** - One replica node is designated as the _leader_. The other nodes are _followers_. Write requests go to the leader node, who will then propagate the changes to the followers.&#x20;
  * **Multi Leader Replication** - This is similar to Single Leader, but now _multiple_ nodes can act as the leader and process write requests.
  * **Leaderless Replication** - _All_ replica nodes can accept write requests from clients, so there is no leader node.&#x20;

#### Single Leader Replication

* One of the replicas is designed as the _leader_. Write requests from clients will be sent to the leader, who will write the new data to it’s local storage.
* The other replicas are known as _followers_. Whenever the leader writes new data to it’s local storage, it also sends the data changes to all of the followers.
* Each follower takes the data change log from the leader and updates its local copy of the database by applying all the new writes.
* When a client wants to read from the database, the read requests can be queried to _any_ of the nodes in the database - leader or follower.
* Writes to the database can be _asynchronous, synchronous, and semi-synchronous:_
  * For an asynchronous write, the leader will get the client’s write request and update it’s own local storage. Then, it will respond saying that the write was successful. _After_ it responds, the leader will send a message to all the follower nodes with the data change from the client’s write request.
  * With a synchronous write, the leader will first make sure _every_ follower node has written the data change to their local database. Once the leader node has received confirmation from all the followers, it will respond with a message that the write was successful.
  * For a semi-synchronous write, the leader will wait for write confirmation from a specific number of follower nodes (this parameter can be configured) until it responds with a message that the write was successful.
* In practice, synchronous writes are rarely used. With a synchronous write strategy, write requests will take an extremely long time (since you have to wait for every follower to respond) and will frequently fail (any time one or more follower nodes are not responsive).

#### Handling Node Outage

* There are two types of node outages: follower outages and leader outages:
  *   Follower Failure: Catch-up recovery

      If a follower node fails, then it can recover quite easily. Followers keep a log of all the data changes received from the leader in local, nonvolatile storage. Therefore, the follower knows the last transaction it processed.

      The follower will query the leader for all the changes that have happened since that last transaction, and then update its local state to match the present.
  *   Leader Failure: Failover

      Handling a failure of the leader is trickier. One of the follower nodes needs to be promoted to the new leader and clients have to be reconfigured to send their writes to the new leader. The other followers also have to start consuming data changes from the new leader.
* Failover processes have tons of things that can go wrong:
  * If asynchronous replication is used, then the new leader may not have received all the writes from the old leader before it failed. This means weaker durability guarantees.
  * When the original leader comes back online, they may be misconfigured to think they are _still_ the leader. This is a common failure and is often called _split brain_.
  * Load issues can arise since your database can’t accept new writes while the failover process is happening. If the leader node fails often, then this can clog up the database.

#### Replication Lag

* When you’re using a single-leader replication strategy with semi-synchronous or asynchronous writes, you’ll frequently run into consistency issues where a client will read stale data from a follower node that hasn’t been fully updated. This inconsistency is a temporary state, and if you wait for a bit then all the followers will eventually catch up. Therefore, this effect is known as _eventual consistency_.
* In order to ease these issues, there are several approaches that you can use to reduce some of the common issues that users face:
  * **Read Your Own Writes** is a solution that guarantees that if the user reloads the page, they will always see any updates they submitted themselves. It makes no promises about other users.
  * **Monotonic Reads** is a guarantee that, once you see data from a follower node, you will always see that data going forward (with no stale data as if "moving back in time").
  * **Consistent Prefix Reads** is a guarantee that if a sequence of writes happens in a certain order, then anyone reading those writes will see them appear in the same order.

### How Notion sharded their Postgres Database

* Notion is an app that is meant to serve as a personal (or corporate) workspace. You can store notes, tasks, wikis, kanban boards and other things in a Notion workspace and you can easily share it with other users.
* Last year, Notion sharded their Postgres monolith into a fleet of horizontally scalable databases. The resulting performance boost was pretty big.
* Sharding a database means partitioning your data across multiple database instances. This allows you to run your database on multiple computers and scale horizontally instead of vertically.

#### When To Shard?

* Sharding your database prematurely can be a big mistake. It can result in an increased maintenance burden, new constraints in application code and little to no performance improvement (so a waste of engineering time).
* The breaking point came when the Postgres VACUUM process began to stall consistently. The VACUUM process clears storage occupied by dead tuples in your database.
* When you update data in Postgres, the existing data is _not_ modified. Instead, a new (updated) version of that data is added to the database. This is because it’s not safe to directly modify existing data, as other transactions could be reading it. This is called Multiversion Concurrency Control (MVCC).
* At a later point, you can run the VACUUM process to delete the old, outdated data and reclaim disk space. If you don’t regularly vacuum your database (or have Postgres run autovacuum, where it does this for you), you’ll _eventually_ reach a transaction ID wraparound failure. So, you _must_ vacuum your database or it **will** eventually fail.

#### Shard Key

* In order to shard a database, you have to pick a shard key. This determines how your data will be split up amongst the shards. You want to pick a shard key that will equally distribute loads amongst all the shards. If one shard is getting a lot more reads/writes than the others, that can make scaling very difficult.

## Resources

### Articles

* [All Modern Database Models in 5 min](https://random-dev.medium.com/databases-in-5-min-bfd3b9bef86) - RandomDev
* [Database Fundamentals](https://tontinton.com/posts/database-fundementals/) - Tony Solomonik
* [Herding elephants: Lessons learned from sharding Postgres at Notion](https://www.notion.so/blog/sharding-postgres-at-notion) - Garrett Fidalgo
* [How Does A Database Work?](https://cstack.github.io/db\_tutorial/) - Connor Stack
* [How Modern SQL Databases Come up with Algorithms that You Would Have Never Dreamed Of](https://www.slideshare.net/LukasEder1/how-modern-sql-databases-come-up-with-algorithms-that-you-would-have-never-dreamed-of) - Lukas Eder
* [Things You Should Know About Databases](https://architecturenotes.co/things-you-should-know-about-databases/) - Mahdi Yusuf

### Books

* [Data Structures for Data-Intensive Applications: Tradeoffs and Design Guidelines](https://cs-people.bu.edu/mathan/publications/fnt23-athanassoulis.pdf) - Manos Athanassoulis, Stratos Idreos, Dennis Shasha
* [Designing Data-Intensive Applications](https://smile.amazon.co.uk/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321/) (Chapter 5) - Martin Kleppman

### GitHub Repositories

* [LearnDB ](https://github.com/spandanb/learndb-py)- Learn database internals by implementing it from scratch

### Images

<figure><img src="../../.gitbook/assets/Most Popular Open Source Databases.gif" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Top 6 Database Models.gif" alt=""><figcaption></figcaption></figure>

### Playgrounds

* [DB Fiddle](https://www.db-fiddle.com/)

### Websites

* [State of Database Survey](https://stateofdb.com/)

### YouTube Videos

* [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw) - Fireship
* [ACID Properties in Databases With Examples](https://www.youtube.com/watch?v=GAe5oB742dw) - ByteByteGo
