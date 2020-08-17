[MongoDB CRUD Operations](https://docs.mongodb.com/manual/crud/) > [MongoDB CRUD Concepts](https://docs.mongodb.com/manual/core/crud/) > Read Isolation, Consistency, and Recency



# Read Isolation, Consistency, and Recency 读隔离性，一致性与近因性

>  On this page 在本页面中
>
> - [Isolation Guarantees 隔离性保证](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#isolation-guarantees)
> - [Monotonic Writes 单调写入](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#monotonic-writes)
> - [Real Time Order 实时顺序](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#real-time-order)
> - [Causal Consistency 因果一致性](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency)

## Isolation Guarantees 隔离性保证

### Read Uncommitted 读未提交

Depending on the read concern, clients can see the results of writes before the writes are [durable](https://docs.mongodb.com/manual/reference/glossary/#term-durable):<br>根据设置的读关注，客户端可以在写入被持久化之前看到写入的结果：

- Regardless of a write’s [write concern](https://docs.mongodb.com/manual/reference/write-concern/), other clients using [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") or [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") read concern can see the result of a write operation before the write operation is acknowledged to the issuing client.<br>不考虑一个写操作的写关注，其他客户端使用`local`或者`available`的读关注级别都可以看到写操作的结果。
- Clients using [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") or [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") read concern can read data which may be subsequently [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/) during replica set failovers.<br>使用`local`或`available`读取关注级别的客户端可以读取数据，这些数据随后可能会在副本集故障转移期间回滚。

For operations in a [multi-document transaction](https://docs.mongodb.com/manual/core/transactions/), when a transaction commits, all data changes made in the transaction are saved and visible outside the transaction. That is, a transaction will not commit some of its changes while rolling back others.<br>对于多文档事务中的操作，当事务提交时，在事务中进行的所有数据更改都将保存并在事务外部可见。也就是说，一个事务在回滚其他事务时将不会提交其某些更改。

Until a transaction commits, the data changes made in the transaction are not visible outside the transaction.<br>在提交事务之前，在事务外部看不到在事务中进行的数据更改。

However, when a transaction writes to multiple shards, not all outside read operations need to wait for the result of the committed transaction to be visible across the shards. For example, if a transaction is committed and write 1 is visible on shard A but write 2 is not yet visible on shard B, an outside read at read concern [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") can read the results of write 1 without seeing write 2.<br>但是，当事务写入多个分片时，并非所有外部读取操作都需要等待已提交事务的结果在所有分片上可见。例如，如果提交了一个事务，并且在分片A上可以看到写1，但是在分片B上还看不到写2，则外部一个读关注级别为`local`的读操作可以读取写1的结果而看不到写2。

Read uncommitted is the default isolation level and applies to [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) standalone instances as well as to replica sets and sharded clusters.<br>读未提交是默认的隔离级别，适用于mongod独立实例以及副本集和分片群集。

### Read Uncommitted And Single Document Atomicity 读未提交与单文档原子性

Write operations are atomic with respect to a single document; i.e. if a write is updating multiple fields in the document, a read operation will never see the document with only some of the fields updated. However, although a client may not see a *partially* updated document, read uncommitted means that concurrent read operations may still see the updated document before the changes are made [durable](https://docs.mongodb.com/manual/reference/glossary/#term-durable).<br>对于单个文档，写操作是原子性的。 即，如果写操作正在更新文档中的多个字段，则读操作将永远不会看到仅更新了某些字段的文档。 但是，尽管客户端可能看不到部分更新的文档，但读未提交意味着并发读取操作仍可以在使更改持久之前看到更新的文档。

With a standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instance, a set of read and write operations to a single document is serializable. With a replica set, a set of read and write operations to a single document is serializable *only* in the absence of a rollback.<br>对于以独立模式部署的mongod实例，对单个文档的一组读写操作是线性的。 使用副本集时，*只有*在没有回滚的情况下，对单个文档的一组读取和写入操作才是线性的。

### Read Uncommitted And Multiple Document Write 读未提交与多文档写入

When a single write operation (e.g. [`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany)) modifies multiple documents, the modification of each document is atomic, but the operation as a whole is not atomic.<br>当单个写入操作（例如`db.collection.updateMany()`）修改多个文档时，每个文档的修改都是原子的，但整个操作不是原子的。

When performing multi-document write operations, whether through a single write operation or multiple write operations, other operations may interleave.<br>当执行多文档写操作时，无论是通过单个写操作还是通过多个写操作，其他操作都可能会交错。

For situations that require atomicity of reads and writes to multiple documents (in a single or multiple collections), MongoDB supports multi-document transactions:<br>对于需要原子性地读写多个文档（在单个或多个集合中）的情况，MongoDB支持多文档事务：

- **In version 4.0**, MongoDB supports multi-document transactions on replica sets.<br>4.0版本中，MongoDB支持副本集内的多文档事务。
- **In version 4.2**, MongoDB introduces distributed transactions, which adds support for multi-document transactions on sharded clusters and incorporates the existing support for multi-document transactions on replica sets.<br>4.2版本中，MongoDB引入了分布式事务，从而增加了对分片群集上多文档事务的支持，并结合了对副本集上多文档事务的现有支持。

For details regarding transactions in MongoDB, see the [Transactions](https://docs.mongodb.com/manual/core/transactions/) page.<br>关于MongoDB事务的细节，请参考【事务】页。

> **IMPORTANT 重要**
>
> In most cases, multi-document transaction incurs a greater performance cost over single document writes, and the availability of multi-document transactions should not be a replacement for effective schema design. For many scenarios, the [denormalized data model (embedded documents and arrays)](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-embedding) will continue to be optimal for your data and use cases. That is, for many scenarios, modeling your data appropriately will minimize the need for multi-document transactions.<br>在大多数情况下，与单文档写入相比，多文档事务产生的性能成本更高，并且多文档事务的可用性并不能替代有效的架构设计。 在许多情况下，非结构化化数据模型（嵌入式文档和数组）将继续是您的数据和用例的最佳选择。 也就是说，在许多情况下，适当地对数据建模将最大程度地减少对多文档交易的需求。
>
> For additional transactions usage considerations (such as runtime limit and oplog size limit), see also[Production Considerations](https://docs.mongodb.com/manual/core/transactions-production-consideration/).<br>关于其他事务使用方面的注意事项（比如运行时显示和oplog大小限制等），请参考【生产注意事项】

Without isolating the multi-document write operations, MongoDB exhibits the following behavior:<br>
在不隔离多文档写入操作的情况下，MongoDB表现出以下行为：

1. Non-point-in-time read operations. Suppose a read operation begins at time *t*1 and starts reading documents. A write operation then commits an update to one of the documents at some later time *t*2. The reader may see the updated version of the document, and therefore does not see a point-in-time snapshot of the data.<br>**非时间点(`Non-point-in-time`)读取操作**。假设读取操作在时间t1开始并开始读取文档。然后，写操作在稍后的某个时间t2提交对其中一个文档的更新。读操作可能会看到文档的更新后版本，因此看不到数据的point-in-time快照。
2. Non-serializable operations. Suppose a read operation reads a document *d*1 at time *t*1 and a write operation updates *d*1 at some later time *t*3. This introduces a read-write dependency such that, if the operations were to be serialized, the read operation must precede the write operation. But also suppose that the write operation updates document *d*2 at time *t*2 and the read operation subsequently reads *d*2 at some later time*t*4. This introduces a write-read dependency which would instead require the read operation to come *after*the write operation in a serializable schedule. There is a dependency cycle which makes serializability impossible.<br>**非可序列化的操作**。假设读取操作在时间t1读取文档d1，而写入操作在稍后的时间t3更新d1。这引入了读写依赖性，因此，如果要序列化操作，则读取操作必须先于写入操作。但是还假设写操作在时间t2更新文档d2，而读取操作随后在稍后的时间t4读取d2。这就引入了写-读依赖关系，它将要求读操作在可序列化时间表中在写操作之后进行。有一个依赖循环，使可序列化成为不可能。
3. Reads may miss matching documents that are updated during the course of the read operation.<br>读取操作可能会丢失在读取操作过程中更新的匹配文档。

### Cursor Snapshot 游标快照

MongoDB cursors can return the same document more than once in some situations. As a cursor returns documents other operations may interleave with the query. If some of these operations change the indexed field on the index used by the query; then the cursor will return the same document more than once.<br>在某些情况下，MongoDB游标可以多次返回同一个文档。 当游标返回文档时，其他操作可能会与查询交错。 如果其中某些操作更改了查询使用的索引上的索引字段； 那么光标将多次返回同一文档。

If your collection has a field or fields that are never modified, you can use a *unique* index on this field or these fields so that the query will return each document no more than once. Query with [`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint) to explicitly force the query to use that index.<br>如果您的集合中有一个或多个从未修改过的字段，则可以在此字段或这些字段上使用唯一索引，这样查询将最多返回每个文档一次。 使用`hint()`查询可显式强制查询使用该唯一索引。

## Monotonic Writes 单调写入

MongoDB provides monotonic write guarantees, by default, for standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances and replica set.<br>**默认的，对于standalone和副本集，MongoDB提供单调写入保证。**

For monotonic writes and sharded clusters, see [Causal Consistency](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency).<br>对于分片集群的单调写入，参考【因果一致性】



## Real Time Order 实时顺序

*New in version 3.4.* *3.4后新引入*

For read and write operations on the primary, issuing read operations with [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern and write operations with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern enables multiple threads to perform reads and writes on a single document as if a single thread performed these operations in real time; that is, the corresponding schedule for these reads and writes is considered linearizable.<br>对于主节点上的读取和写入操作，如果将读关注设置为`linearizable`，将写关注设置为`majority`，那么这种读写模型组合可以使多个线程可以在单个文档上执行读写操作，就好像单个线程实时执行了这些操作一样 ; 也就是说，这些读写的相应计划被认为是线性的。

SEE ALSO 亦可参考：

[Causal Consistency 因果一致性](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency)



## Causal Consistency 因果一致性

*New in version 3.6.*

If an operation logically depends on a preceding operation, there is a causal relationship between the operations. For example, a write operation that deletes all documents based on a specified condition and a subsequent read operation that verifies the delete operation have a causal relationship.<br>如果操作在逻辑上取决于先前的操作，则这些操作之间存在因果关系。 例如，基于指定条件删除所有文档的写入操作和验证删除操作的后续读取操作具有因果关系。

With causally consistent sessions, MongoDB executes causal operations in an order that respect their causal relationships, and clients observe results that are consistent with the causal relationships.<br>在因果一致的会话中，MongoDB按照尊重因果关系的顺序执行因果操作，并且客户观察到与因果关系一致的结果。

### Client Sessions and Causal Consistency Guarantees 客户端会话与因果一致性保证

To provide causal consistency, MongoDB 3.6 enables causal consistency in client sessions. A causally consistent session denotes that the associated sequence of read operations with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and write operations with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern have a causal relationship that is reflected by their ordering.**Applications must ensure that only one thread at a time executes these operations in a client session.**<br>为了提供因果一致性，MongoDB 3.6在客户端会话中启用因果一致性。 因果一致的会话表示具有`majority`的读关注级别的读操作和具有`majority`的写关注级别的写操作的关联序列具有因果关系，这由它们的顺序反映出来。 **应用程序必须确保一次只有一个线程在客户端会话中执行这些操作**。

For causally related operations:<br>对于因果相关的操作：

1. A client starts a client session. 客户端开始一个客户端会话

   > **IMPORTANT 重要**
   >
   > Client sessions only guarantee causal consistency for:<br>客户端会话仅在以下情况下保证因果一致性：
   >
   > - Read operations with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority"); i.e. the return data has been acknowledged by a majority of the replica set members and is durable.<br>读取操作的读关注级别为`majority`；即返回数据已被大多数副本集成员确认并且是持久化的。
   >
   > - Write operations with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern; i.e. the write operations that request 
   >
   >   acknowledgement that the operation has been applied to a majority of the replica set’s voting members.<br>写操作的写关注级别为`majority`；即要求确认该操作已应用于副本集中大多数可投票成员。
   >
   >   For more information on causal consistency and various read and write concerns, see [Causal Consistency and Read and Write Concerns](https://docs.mongodb.com/manual/core/causal-consistency-read-write-concerns/).<br>关于因果一致性和多种读关注级别/写关注级别，请参考【因果一致性和读/写关注级别】

2. As the client issues a sequence of read with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and write operations (with[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern), the client includes the session information with each operation.<br>当客户端发出具有`majority`读关注和`majority`写关注的读取序列时，客户端将会话信息包含在每个操作中。

3. For each read operation with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and write operation with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern associated with the session, MongoDB returns the operation time and the cluster time, even if the operation errors. The client session keeps track of the operation time and the cluster time.<br>对于与会话相关联的每个具有`majority`读关注的读取操作和具有`majority`写关注的写入操作，即使操作出错，MongoDB也会返回操作时间和集群时间。 客户端会话跟踪操作时间和群集时间。

   > **NOTE 注意**
   >
   > MongoDB does not return the operation time and the cluster time for *unacknowledged* (`w: 0`) write operations. Unacknowledged writes do not imply any causal relationship.<br>对于未确认的`（w：0）`写操作，MongoDB不返回操作时间和群集时间。未经确认的写入并不表示任何因果关系。
   >
   > Although, MongoDB returns the operation time and the cluster time for read operations and acknowledged write operations in a client session, only the read operations with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and write operations with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern can guarantee causal consistency. For details, see [Causal Consistency and Read and Write Concerns](https://docs.mongodb.com/manual/core/causal-consistency-read-write-concerns/).<br>尽管MongoDB在客户端会话中返回读操作和已确认写操作的操作时间和集群时间，但是只有具有`majority`读关注的读取操作和具有`majority`写关注的写入操作才能保证因果一致性。 有关详细信息，请参见【因果一致性和读/写关注级别】。

4. The associated client session tracks these two time fields.<br>相关的客户端会话会跟踪这两个时间字段。

   >  **NOTE 注意**
   >
   > Operations can be causally consistent across different sessions. MongoDB drivers and the [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell provide the methods to advance the operation time and the cluster time for a client session. So, a client can advance the cluster time and the operation time of one client session to be consistent with the operations of another client session.<br>不同会话之间的操作可以因果一致。 MongoDB驱动程序和mongo Shell提供了推进客户端会话的操作时间和集群时间的方法。 因此，客户端可以推进一个客户端会话的群集时间和操作时间，使其与另一客户端会话的操作保持一致。



#### Causal Consistency Guarantees 因果一致性保证

The following table lists the causal consistency guarantees provided by causally consistent sessions for read operations with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and write operations with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern.<br>下表列出了因果一致会话为具有`majority`读关注的读取操作和具有`majority`写关注点的写入操作提供的因果一致性保证。

| Guarantees                 | Description                                                  |
| :------------------------- | :----------------------------------------------------------- |
| Read your writes 写后读    | Read operations reflect the results of write operations that precede them.<br>读操作可以正确读到之前写的结果。 |
| Monotonic reads 单调读     | Read operations do not return results that correspond to an earlier state of the data than a preceding read operation.<br>多个读操作会返回一样的<br>For example, if in a session: 比如在下面这个会话中“<br>- write1 precedes write2, 写操作1在写操作2前，<br>- read1 precedes read2, and 读操作1在读操作2前，并且，<br>- read1 returns results that reflect write2 读操作1返回了写操作2的结果<br>then read2 cannot return results of write1. 那么读操作2并不会返回写操作1的结果 |
| Monotonic writes 单调写    | Write operations that must precede other writes are executed before those other writes.<br>写操作按顺序进行。<br>For example, if write1 must precede write2 in a session, the state of the data at the time of write2 must reflect the state of the data post write1. Other writes can interleave between write1 and write write2, but write2 cannot occur before write1.<br>比如，如果会话中写操作1在写操作2前，数据在写操作2的状态必须是写操作1完成后的状态。其他写入操作可以在写操作1和写操作2之间进行交错，但写操作2不可能在写操作1之前进行。 |
| Writes follow reads 读后写 | Write operations that must occur after read operations are executed after those read operations. That is, the state of the data at the time of the write must incorporate the state of the data of the preceding read operations.<br>写操作在读操作后执行。 即，写入时的数据状态必须包含之前的读取操作的数据状态。 |

#### Read Preference 读偏好

These guarantees hold across all members of the MongoDB deployment. For example, if, in a causally consistent session, you issue a write with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern followed by a read that reads from a secondary (i.e. read preference [`secondary`](https://docs.mongodb.com/manual/core/read-preference/#secondary)) with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern, the read operation will reflect the state of the database after the write operation.<br>这些保证适用于MongoDB部署的所有成员。 例如，如果在因果关系一致的会话中发出具有`majority`写关注级别的写操作，然后发出一个具有`majority`读关注级别的从节点（即，读偏好为`secondary`）读操作，则读取操作将反映写入操作后的数据库状态。

#### Isolation 隔离性

Operations within a causally consistent session are not isolated from operations outside the session. If a concurrent write operation interleaves between the session’s write and read operations, the session’s read operation may return results that reflect a write operation that occurred *after* the session’s write operation.<br>**因果一致的会话内的操作与会话外的操作不是隔离的**。 如果并发写操作在会话的写操作和读取操作之间交错，则会话的读操作可能返回反映会话写操作之后发生的写操作的结果。

### MongoDB Drivers MongoDB驱动

> TIP 提示：
>
> Applications must ensure that only one thread at a time executes these operations in a client session.<br>应用程序必须确保一次只有一个线程在客户端会话中执行这些操作。

Clients require MongoDB drivers updated for MongoDB 3.6 or later:<br>客户端需要使用MongoDB 3.6或更高版本的MongoDB驱动程序：

| Java 3.6+   | C# 2.5+   | Perl 2.0+  |
| ----------- | --------- | ---------- |
| Python 3.6+ | Node 3.0+ | PHPC 1.4+  |
| C 1.9+      | Ruby 2.5+ | Scala 2.2+ |



### Examples 示例

> **IMPORTANT 重要**
>
> Causally consistent sessions can only guarantee causal consistency for reads with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern and writes with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern.<br>因果一致性会话只能保证对于读关注级别为`majority`以及写关注级别为`majority`的读取操作的因果一致性。

Consider a collection `items` that maintains the current and historical data for various items. Only the historical data has a non-null `end` date. If the `sku` value for an item changes, the document with the old `sku` value needs to be updated with the `end` date, after which the new document is inserted with the current `sku` value. The client can use a causally consistent session to ensure that the update occurs before the insert.<br>考虑一个维护各种项目的当前和历史数据的`items`集合。 只有历史数据的`end`日期为非空。 如果项目的`sku`值更改，则具有旧`sku`值的文档需要使用`end`日期进行更新，此后，将使用当前`sku`值插入新文档。 客户端可以使用因果一致的会话来确保更新在插入之前发生。

(以python为例，其他实例查看原链接)

```
with client.start_session(causal_consistency=True) as s1:
    current_date = datetime.datetime.today()
    items = client.get_database(
        'test', read_concern=ReadConcern('majority'),
        write_concern=WriteConcern('majority', wtimeout=1000)).items
    items.update_one(
        {'sku': "111", 'end': None},
        {'$set': {'end': current_date}}, session=s1)
    items.insert_one(
        {'sku': "nuts-111", 'name': "Pecans",
         'start': current_date}, session=s1)
```

If another client needs to read all current `sku` values, you can advance the cluster time and the operation time to that of the other session to ensure that this client is causally consistent with the other session and read after the two writes:<br>如果另一个客户端需要读取所有当前的sku值，则可以将集群时间和操作时间推进到另一个会话的集群时间和操作时间，以确保该客户端与另一个会话有因果关系，并在两次写入之后读取：

```
with client.start_session(causal_consistency=True) as s2:
    s2.advance_cluster_time(s1.cluster_time)
    s2.advance_operation_time(s1.operation_time)

    items = client.get_database(
        'test', read_preference=ReadPreference.SECONDARY,
        read_concern=ReadConcern('majority'),
        write_concern=WriteConcern('majority', wtimeout=1000)).items
    for item in items.find({'end': None}, session=s2):
        print(item)
```



### Limitations 限制

The following operations that build in-memory structures are not causally consistent:<br>以下生成内存数据结构的操作并不是因果一致性的：

| Operation 操作                                               | Notes 备注                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats) |                                                              |
| [`$collStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/collStats/#pipe._S_collStats) with `latencyStats` option. |                                                              |
| [`$currentOp`](https://docs.mongodb.com/manual/reference/operator/aggregation/currentOp/#pipe._S_currentOp) | Returns an error if the operation is associated with a causally consistent client session.<br>如果操作和一个因果一致性的客户端会话相关，则会返回错误。 |
| [`createIndexes`](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes) |                                                              |
| [`dbHash`](https://docs.mongodb.com/manual/reference/command/dbHash/#dbcmd.dbHash) | Starting in MongoDB 4.2<br>MongoDB4.2以后的版本才支持        |
| [`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats) |                                                              |
| [`getMore`](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore) | Returns an error if the operation is associated with a causally consistent client session.<br/>如果操作和一个因果一致性的客户端会话相关，则会返回错误。 |
| [`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats) |                                                              |
| [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) | Starting in MongoDB 4.2<br/>MongoDB4.2以后的版本才支持       |
| [`ping`](https://docs.mongodb.com/manual/reference/command/ping/#dbcmd.ping) | Returns an error if the operation is associated with a causally consistent client session.<br/>如果操作和一个因果一致性的客户端会话相关，则会返回错误。 |
| [`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus) | Returns an error if the operation is associated with a causally consistent client session.<br/>如果操作和一个因果一致性的客户端会话相关，则会返回错误。 |
| [`validate`](https://docs.mongodb.com/manual/reference/command/validate/#dbcmd.validate) | Starting in MongoDB 4.2<br/>MongoDB4.2以后的版本才支持       |



原文链接：https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#

译者：刘翔
