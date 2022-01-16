# Databases

## Notes

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

## Resources

### Articles

* [All Modern Database Models in 5 min](https://random-dev.medium.com/databases-in-5-min-bfd3b9bef86) - RandomDev
* [How Does A Database Work?](https://cstack.github.io/db\_tutorial/) - Connor Stack
* [How Modern SQL Databases Come up with Algorithms that You Would Have Never Dreamed Of](https://www.slideshare.net/LukasEder1/how-modern-sql-databases-come-up-with-algorithms-that-you-would-have-never-dreamed-of) - Lukas Eder

### Books

* [Designing Data-Intensive Applications](https://smile.amazon.co.uk/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321/) (Chapter 5) - Martin Kleppman
