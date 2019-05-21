# Chapter 5 Replication
The reason why need replication:
* To keep data geographically close to your users
* To allow the system to continue working even if some of its parts have failed
* To scale out the number of machines that can serve read queries(increase read throughput)

All of the difficulty in replication lies in *handling changes* to replicated data, that's what this chapter is about. This book discuss the popular changes between nodes: *single-leader*, *multi-leader*, and *leaderless* replication.

## Leaders and Followers
Every writes to the database need to be processed by every replica; othersise the replica would no longer contain the same data. The most common solution for this is called *leader-based replication*.
1. One the the replica is designated the leader. When the clients want to write the database, they must send their requests to the leader, which first writes the new data to its local storage.
2. The other replicas are known as *followers*. Whenever the leader writes new data to its local storage, it also sends the data change to all of its followers as part of the replication log or change stream. Each follower takes the log from the leader and updates its local copy of the data base accordingly, by applying all writes in the same order as they were processed on the leader.
3. When a client wants to read from the database, it can query either the leader or any of the followers. However, writes are only accepted on the leader.

This is also called (read-write split???). How to ensure the consistency, there must be some time gap.

### Synchronous Versus Asynchronous Replication
There are two ways when doing the replication. One is synchronous and another is asynchronous. The leader has multiple options:
1. All followers are synchronous replication. As long as all replicas are finished, the leader can accept new write requests. The advantage is that data consistency is guaranteed. The disadvantage is that it's inefficient as the leader has to wait for other followers finish and there is no time guaranteed. This is actually impractical, when some of the followers are down or recover from failure.
2. All followers are asynchronous. The leader send the replication request to followers and then continue handle other writes. This is apparently opposite to 1st. Actually, this is widely used. However, it may has possibility of data loss.
3. Another option is that the leader will only wait for one of the follower finish and then continue handle other write request without waiting for all followers finish. This is called semi-synchronous.

### Setting Up New Followers
1. Leader node cut a snapshot.
2. Copy the snapshot to the new node.
3. The new follower connect to the leader node and requests all change from that snapshot. This require the snapshot associates with a position of the replication log. The name varies, *bin log* in MySQL.
4. The new follower has processed the backlog of the data change. It's done.

### Handling Node Outage

### Implementation of Replication Logs
** Statement-based replication **
This is the original database write queries. The disadvantage is that it require the statement are deterministic, no such like `NOW()` and `RANDOM()` function.

** Write-ahead log(WAL) shipping **
This is physical log that used to recover from failure. This is seem like a minor Implementation detail. The disadvantage is that it's difficult to handle version change.

**Logical(row-based) log replication **
Use different log format than WAL which can decouple the storage engine internals and replication log.

A logical log from a relational database is usually a sequence of records describing writes to database table at the granularity of a row:
* From an inserted row, the log contains the new values of all columns
* For a deleted row, the log contains enough information to uniquely identify the row that was deleted.
* From an updated row, the log contains enough information to uniquely identify the updated row and the new values of all columns

A transaction that modifies several rows generates several such log records, followed by a record indicating that the transaction was committed. MySQL's binlog(when configured to use row0based replication) use this approach.

Since the logical log is decoupled from the storage engine internals, it can more easily be kept backwad compatible, allowing the leader and follower to run different versions of the database software, or even different storage engines. 


## Problems with Replication Lag

### Reading Your Own Writes

### Monotonic Reads

### Consistent Prefix Reads

### Solutions for Replication Lag

## Multi-Leader Replication

### Use Cases for Multi-Leader Replication

### Handling Write Conflicts

### Multi-Leader Replication Topologies

## Leaderless Replication

### Writing to the Database When a Node is Down

### Limitations of Quorum Consistency

### Sloppy Quorums and Hinted Handoff

### Detecting Concurrent Writes

## Summary
