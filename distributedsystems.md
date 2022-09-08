# Distributed Systems

## Refs

- [https://martinfowler.com/articles/patterns-of-distributed-systems/]

## Why are they hard?

- Processes can crash (see WAL)
- Processes can pause arbitrarily (e.g garbage collection). Generation Clock is used to mark and detect requests from older leaders
- Networks can deliver traffic late
- Networks may never deliver (see heartbeat, split brain/quorum/leader-followers)
- We want our application to have a single view of the data

## WAL - write ahead log

- Once a server agrees to perform an action, it should do so even if it fails and restarts losing all of its in-memory state
- Changes written to disk log file before applied to database

### Low-Water Mark

- An index in the WAL: showing which portion of the log can be discarded
- Most consensus implementations like Zookeeper, or etcd (as defined in RAFT), implement snapshot mechanisms
- The storage engine takes periodic snapshots and it stores the log index which is successfully applied

### High-Water Mark

- An index in the write ahead log showing the last successful replication to followers

## CAP

- Consistency - every read receives the most recent write, or every server should return the same data at the same time
- Availability - every request receives a response that is not an error
- Partition tolerance - the system continues to operate despite network disconnections/delays between nodes

- Consistency vs. Availability (when a network partition)
- ACID (stronger consistency) databases choose consistency over availability
- BASE (weaker consistency) systems choose availability over consistency
  - Basic Availability: appears to work most of the time
  - Soft-state: without consistency guarantees, after some amount of time, we only have some probability of knowing the state
  - Eventual consistency: consistency at some later point (e.g., lazily at read time)
  - e.g. Document stores, NoSQL, neo4J

## Transactions

[https://github.com/mishnit/mishnit.github.io/blob/master/designs/notes/transactions.md]

Transactions are where *several* reads and writes are grouped together
into a single logical unit to execute as one operation.

Whole transaction either succeeds or fails.

Trade-off between isolation guarantees and performance/availability

- Atomicity = abortability - all writes discarded on error
- Consistency = keep indexes and foreign keys in sync with table
- Isolation = concurrent transactions cannot intefer with each other.  See isolation levels
- Durability = data written not forgotten, ie. written to disk

### Problems

### Non-repeatable reads (fixed by Snapshot Isolation)

- Non-repeatable reads are when your transaction reads committed UPDATES from another transaction
- The same row now has different values than it did when your transaction began
- Problematic when you have two queries of the same value
- "User has binoculars and looks twice at REPEATING merry-go-round horses in the same transaction and sees keeps on seeing different horses going UP(date) and down"

### Phantom reads (fixed by Range Locks)

- Phantom reads are from when reading from committed INSERTS and/or DELETES from another transaction
- There are new rows or rows that have disappeared since you began the transaction
- "See PHANTOM ghost scaring row in or out of database"

### Dirty reads (fixed by Read Committed)

- Dirty read occurs when your transaction does a read, and another transaction updates the same row but doesn't commit the work
- Your transaction sees the uncommitted (dirty) value, which isn't a durable state change
- "See DIRTY row with tyre marks that gets ROLL(back)ed over"

### Isolation levels

Good diagrams:
[https://architecturenotes.co/things-you-should-know-about-databases/]

Weakest to strongest:

### Read Committed Isolation (e.g. row-level locks)

- Reads: only data that has been committed will be returned (no dirty reads)
- Writes: only data that has been committed will be overwritten (no dirty writes)
  e.g. 2 users write to 2 tables with a join - 2nd user needs to wait for
  transaction to complete before starting:
  ![Dirty Writes](./assets/transactions-5.png)
  Use row-level lock to prevent this.

### Range Locks (stronger than row-level locks)

- Lock the range of values captured by the transaction and don't allow inserts or updates within the range captured by the transaction

### Snapshot Isolation (e.g. MVCC)

- Snapshot isolation fixes read skew (AKA "non-repeatable read")  
- Read skew happens when a transaction is writing to multiple objects and in parallel, a second transaction reads the new data in some objects and old data in other objects.
- Other names for Snapshot Isolation:
  - Oracle: serializable isolation
  - PostgreSQL and MySQL: repeatable read isolation.
- Snapshot isolation implemented using multi-version concurrency control (MVCC).
- MVCC: keeps several different committed versions of an object as in-progress transactions need to read database state at different points in time
  - Each transaction is always given a unique, always-incrementing transaction ID
  - Writes are tagged with the writer's transaction ID. Whenever a transaction reads from the database, any writes made by transactions with a later transaction ID are ignored.
  - Garbage collection process periodically removes old object versions
  - Deletes mark objects for deletion by setting the deleted_by field to the transaction ID.

- Lost updates = read-modify-writes clash, so data lost. One of the modifications is lost as the second write does not include the first modification

Phantom reads = new rows are added or removed by another transaction to the records being read

### Serializable Isolation (e.g. 2 Phase Locks)

- Serializable isolation is the strongest isolation level, prevents ALL race conditions
- Guarantees that even if transactions are run in parallel (concurrently), the end result is the same as if they were run one at a time (ie. serially)
- Write skew = decision based on read causes write, but by the time the write is made the read data is out-of-date. Only *serializable isolation* prevents write skew
- It is not important which transaction executes first, only that the result does not reflect any mixing of the transactions
- Implementations:
  - Simplest: use single threading for all transactions - poor performance!
  - Alternatively use pessimistic or optimistic locking
  - Transactions that fail will need to be aborted/retried
  - Two phase locking (MySQL (InnoDB) and SQL Server):
  - Shared lock: any transaction can read but not write
  - Exclusive lock: no other transaction can read or write
  - Reads use shared lock, writes use exclusive lock.  Shared locks wait for exclusive lock.
- It is ok for that serial order to be different from the order in which transactions were actually run

See also [http://www.bailis.org/blog/linearizability-versus-serializability/]
One of the reasons these definitions are so confusing is that:

- Linearizability comes from the distributed systems community
- Serializability comes from the database community
- Strong Consistency < External Consistency < Serializable Isolation
- Serialisability is about multiple transactions made at the same time having same result as single-threaded
- Linearizability is about multiple reads/writes to an object having same result as single-threaded
- Linearizability is about strict time ordering of read and writes operations matching the wall-clock time flow

### Consistency

- Property of distributed systems: every server should have the same view regardless of any client updates
- Strong consistency means that the system converges on a single value, and the client always reads the latest value

### Linearizability = 'recency guarantee'

- Property of concurrent objects that support atomic reads and writes - e.g.
database row or cell
- As one client successfully completes a write, all clients reading from the database must be able to see the same value just written
- If the clients make requests concurrently, the results need to look as if the requests came one by one
- Does not say anything about transactions!
- e.g. ZooKeeper's coordination algorithm, ZooKeeper Atomic Broadcast (ZAB), has linearizability for writes but not reads [https://zookeeper.apache.org/doc/r3.6.2/zookeeperInternals.html]
  - doesn't provide linearizability guarantees for reads, because each ZooKeeper node serves reads locally
  - reads in ZooKeeper are sequentially consistent
  - prioritizes performance over consistency for the read use case
- e.g. Java: reads and writes are not guaranteed to be linearizable unless the modifying variable is `volatile` or if both the write/read are executed
  from within a `synchronized` block:
  - most computer systems use multiple CPU cores, and each core has its own cache
  - write operation might only change the variable in the CPU cache
  - write to main memory requires flush of write-behind CPU cache
  
#### Strict Serializability

- e.g. think of transaction T1 to move $$ checking -> savings, T2 to move $$ from savings
  - Important that we perform T1 THEN T2
- Google Spanner uses "External Consistency" instead of "Strict Serializability"
  - Better performance because reads aren't blocked by writes
  - "Proper Timestamping" returns read with most recent timestamp
  - Only works if timestamps are consistent with the order of commits
  - "TrueTime" provides monotonically increasing timestamps so all servers have correct time without skew
    - Write transactions can have proper timestamp without the need for global communication
    - Strong reads (guarantee most recent data) execute in one round of communication - even if they span multiple servers

## Quorum

- In general, if we want to tolerate f failures we need a cluster size of 2f + 1

## Lamport Logical Clock, 1987

- The algorithm of the logical clock is very simple: Each event corresponds to a Lamport timestamp (the initial value is 0).
- If an event occurs within a node, the timestamp value adds 1.
- If an event is a sending event, the timestamp adds 1, and the timestamp is added to that message.
- If an event is a reception event, the timestamp is Max (local timestamp within a message) plus 1.
- For all associated sending and reception events, this can ensure that the timestamp of a sending event is smaller than that of a reception event
- Not widely used apart from "Generation Clock"
  - "Generation Clock" marks and detect requests from older leaders
  - Maintains a monotonically increasing number indicating the generation of the server. Every time a new leader election happens, it should be marked by increasing the generation
  - The generation needs to be available beyond a server reboot, so it is stored with every entry in the Write-Ahead Log
  - example: ZAB - "Epoch number" is maintained as part of every transaction id. Every transaction persisted in Zookeeper has a generation marked by epoch.

## Paxos, 1990s

Hard to understand

## ZAB, 2007

- Only used in Zookeeper
- Leader elected, Followers
- Writes use 2 Phase Commit: Vote and Accept - more than 50% votes causes commit

## Raft, 2014

- Nodes can be Follower, Candidate or Leader
- All change requests go to leader
- Election: all nodes start as followers, one node becomes candidate - if it gets majority votes -> leader
- Uses Proposers
- Used in Chubby (Google File System, Bigtable), etcd

## Zookeeper Example

- Reads are always answered locally by the node, so no communication with the cluster is involved.
- Writes are forwarded to a designated "Leader" node, which forwards the write-request to all nodes and waits for their replies. If at least half of the nodes answers, the write is considered successful
- Applications should use ZooKeeper for all communication related to the ZooKeeper state, because reads can be stale
- If you need linearizable reads, sync API can be used before your read request to sync the client-side version with the leader

## Lucene

A Lucene index consists of Segments.  Each Segment has:

- Index files, files from each segment have the same prefix `_0`, `_1` etc
- Inverted Index maps term -> documents
- Field Names: field -> metadata (indexed, term vectors enabled, etc)
- Term Dictionary: term -> frequency

## Hashing

- Convert variable length data into fixed size data
- Uniform
- Deterministic
- SHA-256: bitcoin mining uses nonces (random number guesses -> hash are compared a target hash)

## Caching

Example - EHCache, Memcache, Redis (in-memory cache)
Database cache - e.g. leaderboard scores

Consider read/write ratios, e.g.:

- 80-20 rule, i.e., 20% of daily read volume for photos is generating 80% of traffic

- Application server cache:
  - Cache placed on a request layer node -> expand to many nodes
- Distributed cache:
  - Each request layer node *owns* part of the cached data
  - Entire cache is divided up using a *consistent hashing function*, a missing node leads to cache lost
- Global cache:
  - A server or file store that is faster than original store
- Content distributed network:
  - CDN serves that content if it has it locally available
  - If content isn’t available, CDN will query back-end servers for the file
- Cache invalidation
  - *Write-through* cache: Data is written into the cache and permanent storage at the same time.
    Higher latency for write operations
  - *Write-around* cache: Data is written to permanent storage, not cache. Query for recently
    written data creates a cache miss
  - *Write-back* cache: Data is only written to cache, write to the permanent storage is done later on.
    Risk of data loss in case of system disruptions.
- Cache eviction policies:
  - FIFO: first in first out - queue?
  - LIFE: last in first out - queue?
  - LRU: least recently used - LinkedHashMap is doubly-linked list with insertion-order so can remove old entries
  - MRU: most recently used - LinkedHashMap is doubly-linked list with insertion-order so can remove new entries
  - LFU: least frequently used - HashMap with frequency count?
  - RR: random replacement - HashMap with hashing function
- CPU Cores have L1 64KB/L2 256KB cache each, and one shared L3 16MB cache
- Direct mapped cache: each line of cache can only cache a specific location in main memory and that is a cheaper solution
- Fully associated cache means that a cache line can cache any location in main RAM, more expensive
- Set associative cache offers a compromise

## Load Balancing

- Horizontal scaling (add more servers)
- LB locations: database, application front end
- Metrics: Least connection, Least response time, Round robin
- Elastic Load Balancer - Healthchecks, Sticky Sessions
- L4 is TCP/UDP - only knows no. connections and response times. Terminate TLS
- L7 is HTTP - knows about URLs, content type, cookies.  Can do redirects. Terminate HTTPS

## Paritioning

- Methods:
  - Horizontal/Range based sharding - different rows into different tables
  - Vertical/Feature based sharding - specific feature to their own server
  - Directory-based partitioning - service that knows the partitioning scheme and abstracts it away from the database access code (SPOF)
- Criteria:
  - Key or hash-based - Adding new servers may require changing the hash function, which would need redistribution of all data. Solution is
    Consistent Hashing: only `n/m` keys need to be remapped on average where `n` is the number of keys and `m` is the number of slots
  - Round-robin partitioning (`i % n`)
  - Composite partitioning (combo of above)

- Issues:
  - Joins and denormalization: data has to be compiled from multiple servers. Workaround: denormalize
  - Referential integrity
  - Rebalancing

## Client-Server Communication

- Ajax Polling
- HTTP Long-Polling
- WebSockets (TCP) - bidirectional
- Server-Sent Event (SSE) - unidirectional

## Proxy

- Forward Proxy: Client -> Internet
- Reverse Proxy: Internet -> Server
- TLS encryption:

## TLS

- SSL 1,2,3
- TLS 1.0, 1.1, 1.2, 1.3
- Certificate Authorities
- Simple encryption: auth is server only
- Mutual encryption: auth is both client and server, with client cert

## Reactive Architecture

- Streams
- Backpressure

## Observability

- The USE method tells you how happy your machines are, the RED method tells you how happy your users are
- Real User Monitoring - measures the performance of a page from real users' machines using script
- Synthentic User Montoring - Lab condtions, run benchmarks

### USE

- Utilization - Percent time the resource is busy, such as node CPU usage
- Saturation - Amount of work a resource has to do, often queue length or node load
- Errors - Count of error events

### RED

- Rate - Requests per second
- Errors - Number of requests that are failing
- Duration - Amount of time these requests take, distribution of latency measurements

### Golden Signals - Google SRE Book

- Latency - Time taken to serve a request
- Traffic - How much demand is placed on your system
- Errors - Rate of requests that are failing
- Saturation - How “full” your system is

## Architecture

- Read:write ratios
- Locality of data: random access vs contiguous
- Consistency: soft-state (BASE), eventual, strong (ACID)
