# Read Concern

# 读关注

On this page

在本页中

- [Read Concern Levels](https://docs.mongodb.com/manual/reference/read-concern/#read-concern-levels)
- [`readConcern` Support](https://docs.mongodb.com/manual/reference/read-concern/#readconcern-support)
- [Considerations](https://docs.mongodb.com/manual/reference/read-concern/#considerations)
- [读关注等级](https://docs.mongodb.com/manual/reference/read-concern/#read-concern-levels)
- [`读关注` 支持](https://docs.mongodb.com/manual/reference/read-concern/#readconcern-support)
- [注意事项](https://docs.mongodb.com/manual/reference/read-concern/#considerations)

The `readConcern` option allows you to control the consistency and isolation properties of the data read from replica sets and replica set shards.

`读关注` 选项允许你控制从副本集和分片集读取数据的一致性和隔离性。

Through the effective use of [write concerns](https://docs.mongodb.com/manual/reference/write-concern/) and read concerns, you can adjust the level of consistency and availability guarantees as appropriate, such as waiting for stronger consistency guarantees, or loosening consistency requirements to provide higher availability.

通过有效地使用[写关注](https://docs.mongodb.com/manual/reference/write-concern/)和读关注，你可以适当地调整一致性和可用性的保证级别，例如等待以保证更强的一致性，或放松一致性要求以提供更高的可用性。

MongoDB drivers updated for MongoDB 3.2 or later support specifying read concern.

将MongoDB驱动程序更新到MongoDB 3.2或更高版本以支持读关注。

## Read Concern Levels

## 读关注级别

The following read concern levels are available: 

以下为可用的读关注级别：

| `level`                                                      | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") | The query returns data from the instance with no guarantee that the data has been written to a majority of the replica set members (i.e. may be rolled back).<br />查询并从实例返回数据，但不能保证该数据已被写入大多数副本集成员（即可能已经回滚）。<br /><br />**Default for:**<br />reads against primary<br />reads against secondaries if the reads are associated with [causally consistent sessions](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions).<br />**默认为：**<br />针对主节点读。<br />如果读取与因果一致的会话相关联，则针对从节点读。<br /><br />**Availability:** Read concern `local` is available for use with or without causally consistent sessions and transactions.<br />**可用性：**读关注`local`可用于有或没有[因果关系一致的会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)和事务中。<br /><br />For more information, see the [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") reference page.<br />更多的信息，请参考[`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local")页 |
| [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") | The query returns data from the instance with no guarantee that the data has been written to a majority of the replica set members (i.e. may be rolled back).<br />查询并从实例返回数据，但不能保证该数据已被写入大多数副本集成员（即可能已经回滚）。<br /><br />**Default for:** reads against secondaries if the reads are **not** associated with [causally consistent sessions](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions).<br />**默认为：**如果读取与[因果关系一致的会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)没有关联，则针对从节点读<br /><br />**Availability:** Read concern `available` is **unavailable for use** with causally consistent sessions and transactions.<br />**可用性：**读关注`available`无法用于有因果关系一致的会话和事务中。<br /><br />For sharded clusters, [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") read concern provides the lowest latency reads possible among the various read concerns. However, this comes at the expense of consistency as [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") read concern can return [orphaned documents](https://docs.mongodb.com/manual/reference/glossary/#term-orphaned-document) when reading from a sharded collection. To avoid the risk of returning orphaned documents when reading from sharded collections, use a different read concern such as read concern [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local").<br />对于分片群集，[`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available")读关注提供了各种读关注中尽可能最低的延迟。但是，这是以牺牲一致性为代价的，因为从分片的集合中进行读取时，[`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available")读关注会返回[孤立的文档](https://docs.mongodb.com/manual/reference/glossary/#term-orphaned-document)。为了避免从分片的集合中读取时返回孤立文档的风险，可使用其他读关注，如[`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local")读关注。<br /><br />For more information, see the [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") reference page.<br />更多的信息，请参考[`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available")页<br /><br />*New in version 3.6.*<br />3.6版本的新功能 |
| [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") | The query returns the data that has been acknowledged by a majority of the replica set members. The documents returned by the read operation are durable, even in the event of failure.<br />该查询返回大多数副本集成员已确认的数据。读操作返回的文档即使在发生故障的情况下也可用。<br />To fulfill read concern “majority”, the replica set member returns data from its in-memory view of the data at the majority-commit point. As such, read concern [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") is comparable in performance cost to other read concerns.<br />为了满足读关注“majority”，副本集成员从其内存视图中返回多数提交点提交的数据。这样，读关注[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")在性能成本上可与其他读关注相媲美。<br /><br />**Availability:**<br />Read concern [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") is available for use with or without causally consistent sessions and transactions.<br />You can disable read concern [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") for a deployment with a three-member primary-secondary-arbiter (PSA) architecture; however, this has implications for change streams (in MongoDB 4.0 and earlier only) and transactions on sharded clusters. For more information, see [Disable Read Concern Majority](https://docs.mongodb.com/manual/reference/read-concern-majority/#disable-read-concern-majority).<br />**可用性：**<br />读关注[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")可用于有或没有因果关系一致的会话和事务中。<br />对于具有三名成员的主从仲裁（PSA）架构的部署，可以禁用读关注[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")；但是，这对change streams（仅在MongoDB 4.0和更早版本中）和分片群集上的事务有影响。有关更多信息，请参见[禁用读关注Marjority](https://docs.mongodb.com/manual/reference/read-concern-majority/#disable-read-concern-majority).。<br /><br />**Requirements:** To use [read concern](https://docs.mongodb.com/manual/reference/glossary/#term-read-concern) level of [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority"), replica sets must use [WiredTiger storage engine](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger).<br />**要求：**若要使用[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")的[读关注](https://docs.mongodb.com/manual/reference/glossary/#term-read-concern)级别，副本集必须使用WiredTiger存储引擎。<br /><br />**NOTE：**<br />For operations in [multi-document transactions](https://docs.mongodb.com/manual/core/transactions/), read concern `"majority"` provides its guarantees only if the transaction commits with [write concern “majority”](https://docs.mongodb.com/manual/core/transactions/#transactions-write-concern). Otherwise, the [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read concern provides no guarantees about the data read in transactions.<br />**注意：**<br />对于[多文档事务](https://docs.mongodb.com/manual/core/transactions/)中的操作，仅当事务以[写关注`"majority"`](https://docs.mongodb.com/manual/core/transactions/#transactions-write-concern)提交时，读关注[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")才提供其保证。否则，[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")读关注不能保证其在事务中读取的数据。<br /><br />For more information, see the [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") reference page.<br />更多的信息，请参考[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")页 |
| [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") | The query returns data that reflects all successful majority-acknowledged writes that completed prior to the start of the read operation. The query may wait for concurrently executing writes to propagate to a majority of replica set members before returning results.<br />该查询返回的数据表示了这些数据在写操作开始之前已成功在大多数节点确认写入。查询可能会等待并发执行的写操作传播到大多数副本集成员，然后返回结果。<br />If a majority of your replica set members crash and restart after the read operation, documents returned by the read operation are durable if [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) is set to the default state of `true`.<br />如果大多数副本集成员崩溃并在读操作后重新启动，则如果将[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault)设置为默认状态true，则读操作返回的文档将还是有效的。<br />With [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) set to `false`, MongoDB does not wait for [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") writes to be written to the on-disk journal before acknowledging the writes. As such, `majority` write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set.<br />将[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault)设置为`false`时，MongoDB在没确认写入之前不会等待[`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写入磁盘日志。这样，如果给定副本集中大多数节点的瞬时丢失（例如崩溃和重新启动），`majority`写操作可能会回滚。<br /><br />**Availability:**<br />Read concern [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") is **unavailable for use** with causally consistent sessions and transactions.<br />You can specify linearizable read concern for read operations on the [`primary`](https://docs.mongodb.com/manual/reference/replica-states/#replstate.PRIMARY) only.<br />**可用性：**<br />读关注[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")不适用于因果一致的会话和事务。<br />你可以仅对主节点上的读操作指定为线性读关注。<br /><br />You cannot use the [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) or the [`$merge`](https://docs.mongodb.com/manual/reference/operator/aggregation/merge/#pipe._S_merge) stage in conjunction with read concern [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable"). That is, if you specify [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern for [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate), you cannot include either stages in the pipeline.<br />你不能将[`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out)或[`$merge`](https://docs.mongodb.com/manual/reference/operator/aggregation/merge/#pipe._S_merge)操作与读关注[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")结合使用。也就是说，如果将[`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)指定为[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")读关注，则不能在管道中使用任何的操作。<br /><br />**Requirements:** Linearizable read concern guarantees only apply if read operations specify a query filter that uniquely identifies a single document.<br />**要求：**linearizable读关注仅保证在读操作指定了唯一标识单个文档的查询过滤器时可用。<br /><br />**TIP：**<br />Always use `maxTimeMS` with linearizable read concern in case a majority of data bearing members are unavailable. `maxTimeMS` ensures that the operation does not block indefinitely and instead ensures that the operation returns an error if the read concern cannot be fulfilled.<br />请始终将`maxTimeMS`与linearizable读关注一起使用，以防止大多数数据承载成员不可用。`maxTimeMS`确保操作不会无限期地阻塞，而是确保如果无法满足读取要求，则操作将返回错误。<br />For more information, see the [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") reference page.<br />更多的信息，请参考[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")页 |
| [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") | If the transaction is not part of a [causally consistent session](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions), upon transaction commit with write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority"), the transaction operations are guaranteed to have read from a snapshot of majority-committed data.<br />如果事务不是[因果一致会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)的一部分，写关注为[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")且在事务提交后，可以确保事务操作已从多数提交数据的快照中读取。<br />If the transaction is part of a [causally consistent session](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions), upon transaction commit with write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority"), the transaction operations are guaranteed to have read from a snapshot of majority-committed data that provides causal consistency with the operation immediately preceding the transaction start.<br />如果事务是[因果一致会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)的一部分，写关注为[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")且在事务提交后，可以确保事务操作已从多数提交数据的快照中读取，该快照提供了与紧接事务开始之前的操作的因果一致性。<br />**Availability:**<br />Read concern [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") is only available for use with multi-document transactions.<br />For transactions on a sharded cluster, if any operation in the transaction involves a shard that has [disabled read concern “majority”](https://docs.mongodb.com/manual/reference/read-concern-majority/#disable-read-concern-majority), you cannot use read concern [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") for the transaction. You can only use read concern [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") or [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") for the transaction.<br />**可用性：**<br />读关注[`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot")仅可用于多文档事务。<br />对于分片群集上的事务，如果事务中的任何操作涉及[已被禁用读关注“majority”](https://docs.mongodb.com/manual/reference/read-concern-majority/#disable-read-concern-majority)的分片，那你就不能对该事务使用读关注[`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot")。你只能对事务使用读关注[`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local")或[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")。 |

Regardless of the [read concern](https://docs.mongodb.com/manual/reference/glossary/#term-read-concern) level, the most recent data on a node may not reflect the most recent version of the data in the system.
无论[读关注](https://docs.mongodb.com/manual/reference/glossary/#term-read-concern)级别如何，节点上的最新数据都可能无法反映系统中数据的最新版本

For more information on each read concern level, see:

- [Read Concern "local"](https://docs.mongodb.com/manual/reference/read-concern-local/)
- [Read Concern "available"](https://docs.mongodb.com/manual/reference/read-concern-available/)
- [Read Concern "majority"](https://docs.mongodb.com/manual/reference/read-concern-majority/)
- [Read Concern "linearizable"](https://docs.mongodb.com/manual/reference/read-concern-linearizable/)
- [Read Concern "snapshot"](https://docs.mongodb.com/manual/reference/read-concern-snapshot/)

有关每个阅读关注级别的更多信息，请参见：

- [读关注 "local"](https://docs.mongodb.com/manual/reference/read-concern-local/)
- [读关注 "available"](https://docs.mongodb.com/manual/reference/read-concern-available/)
- [读关注 "majority"](https://docs.mongodb.com/manual/reference/read-concern-majority/)
- [读关注 "linearizable"](https://docs.mongodb.com/manual/reference/read-concern-linearizable/)
- [读关注 "snapshot"](https://docs.mongodb.com/manual/reference/read-concern-snapshot/)

## `readConcern` Support

## 读关注支持

### Read Concern Option

读关注选项

For operations not in [multi-document transactions](https://docs.mongodb.com/manual/core/transactions/), you can specify a `readConcern` level as an option to commands and methods that support read concern:

对于不在[多文档事务](https://docs.mongodb.com/manual/core/transactions/)中的操作，你可以将 `readConcern` 级别指定为一个命令和方法的选项：

```shell
readConcern: { level: <level> }
```

To specify the read concern level for the [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell method [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find), use the [`cursor.readConcern()`](https://docs.mongodb.com/manual/reference/method/cursor.readConcern/#cursor.readConcern) method:

要为 [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell方法 [db.collection.find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) 指定读关注级别，请使用 [cursor.readConcern()](https://docs.mongodb.com/manual/reference/method/cursor.readConcern/#cursor.readConcern) 方法：

```
db.collection.find().readConcern(<level>)
```

### Transactions and Available Read Concerns

事务和可用的读关注

For [multi-document transactions](https://docs.mongodb.com/manual/core/transactions/), you set the read concern at the transaction level, not at the individual operation level. The operations in the transaction will use the transaction-level read concern. Any read concern set at the collection and database level is ignored inside the transaction. If the transaction-level read concern is explicitly specified, the client level read concern is also ignored inside the transaction.

对于[多文档事务](https://docs.mongodb.com/manual/core/transactions/)，应在事务级别而不是在单个操作级别设置读关注。事务中的操作将使用事务级别的读关注。事务内部将忽略在集合和数据库级别设置的任何读关注。如果显式指定了事务级别的读关注点，则在事务内部也将忽略客户端级别的读关注点。

IMPORTANT

重要

Do not explicitly set the read concern for the individual operations. To set the read concern for transactions, see [Read Concern/Write Concern/Read Preference](https://docs.mongodb.com/manual/core/transactions/#transaction-options).

不要为各个操作明确设置读关注。要设置事务的读关注，请参阅读 [Read Concern/Write Concern/Read Preference](https://docs.mongodb.com/manual/core/transactions/#transaction-options)。

You can set the read concern at the transaction start:

你可以在事务开始时设置读关注

- For multi-document transaction, read concern levels [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot"), [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") and [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") are available.
- 对于多文档事务，读关注级别[`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot"), [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") 和 [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")是可用的。
- Write commands that are part of a [multi-document transactions](https://docs.mongodb.com/manual/core/transactions/) can support the transaction-level read concern.
- [多文档事务](https://docs.mongodb.com/manual/core/transactions/)中的写命令可以支持事务级别的读关注。

If unspecified at the transaction start, transactions use the session-level read concern or, if that is unset, the client-level read concern.

如果未在事务开始时指定，则事务将使用会话级的读关注，或者如果未设置，则使用客户端级的读关注。

For more information, see [Transaction Read Concern](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern).

更相关信息，请参考 [事务的读关注](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern).

### Causally Consistent Sessions and Available Read Concerns

### 因果一致的会话和可用的读关注

For operations in a [causally consistent session](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency), [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") and [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") levels are available. However, to guarantee causal consistency, you must use [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority"). For details, see [Causal Consistency](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency).

对于在[因果一致的会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency)中的操作，[`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") h和 [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")级别可用。但是，为了保证因果一致性，你必须使用 [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")。有关详细信息，请参见 [因果一致性](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency)。

If a multi-document transaction is associated with a causally consistent session, [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") is also available for the the transaction.

如果多文档事务与因果一致的会话相关联，则[`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") 也可用于该事务。

### Operations That Support Read Concern

### 支持读关注的操作

The following operations support read concern:

下列的操作支持读关注：

IMPORTANT

重要

To set read concern for operations in a transaction, you set the read concern at the transaction level, not at the individual operation level. Do not explicitly set the read concern for the individual operations in a transaction. For more information, see [Transactions and Read Concern](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern).

在为事务中的操作设置读关注时，请在事务级别而不是在单个操作级别设置读关注。不要在事务中明确的设置单独操作的读关注。更多信息，查看[事务和读关注](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern)

| Command/Method 命令/方法                                     | [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") | [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") | [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") | [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") [[3\]](https://docs.mongodb.com/manual/reference/read-concern/#snapshot) | [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count) | ✓                                                            | ✓                                                            | ✓                                                            |                                                              | ✓                                                            |
| [`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct) | ✓                                                            | ✓                                                            | ✓                                                            | ✓ [[2\]](https://docs.mongodb.com/manual/reference/read-concern/#distinct) | ✓                                                            |
| [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            |
| [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) via [`cursor.readConcern()`](https://docs.mongodb.com/manual/reference/method/cursor.readConcern/#cursor.readConcern) | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            |
| [`geoSearch`](https://docs.mongodb.com/manual/reference/command/geoSearch/#dbcmd.geoSearch) | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            |
| [`getMore`](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore) | ✓                                                            |                                                              |                                                              |                                                              | ✓                                                            |
| [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) | ✓                                                            | ✓                                                            | ✓                                                            | ✓                                                            | ✓ [[1\]](https://docs.mongodb.com/manual/reference/read-concern/#id6) |
| [`Session.startTransaction()`](https://docs.mongodb.com/manual/reference/method/Session.startTransaction/#Session.startTransaction) | ✓                                                            |                                                              | ✓                                                            | ✓                                                            |                                                              |

| [[1\]](https://docs.mongodb.com/manual/reference/read-concern/#id5) | You cannot use the [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) or the [`$merge`](https://docs.mongodb.com/manual/reference/operator/aggregation/merge/#pipe._S_merge) stage in conjunction with read concern [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable"). That is, if you specify [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern for [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate), you cannot include either stages in the pipeline.<br />你不能将[`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) 或者 [`$merge`](https://docs.mongodb.com/manual/reference/operator/aggregation/merge/#pipe._S_merge)阶段与读关注的[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")结合使用。也就是说，如果为[`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)指定[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")读关注，则不能在管道中包括任何一个阶段。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

| [[2\]](https://docs.mongodb.com/manual/reference/read-concern/#id4) | Read concern [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") is available only for multi-document transactions. In a transaction, you cannot use the `distinct` command or its helpers on a sharded collection.<br />读关注[`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot")仅适用于多文档事务。在事务中，不能在分片集合上使用`distinct`命令或其协助命令。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The following write operations can also accept a read concern if part of a multi-document transaction:

下列的写操作也能接受读关注，但必须是多文档事务的一部分：

IMPORTANT

重要

To set read concern for operations in a transaction, you set the read concern at the transaction level, not at the individual operation level.

在为事务中的操作设置读关注时，请在事务级别而不是在单个操作级别设置读关注

| Command 命令                                                 | [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") | [`"available"`](https://docs.mongodb.com/manual/reference/read-concern-available/#readconcern."available") | [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") | [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") [[3\]](https://docs.mongodb.com/manual/reference/read-concern/#snapshot) | [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete)<br />[`db.collection.deleteMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany)<br />[`db.collection.deleteOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne)<br />[`db.collection.remove()`](https://docs.mongodb.com/manual/reference/method/db.collection.remove/#db.collection.remove) | ✓                                                            |                                                              |                                                              | ✓                                                            |                                                              |
| [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)<br />[`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)<br />[`db.collection.findOneAndDelete()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndDelete/#db.collection.findOneAndDelete)<br />[`db.collection.findOneAndReplace()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndReplace/#db.collection.findOneAndReplace)<br />[`db.collection.findOneAndUpdate()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndUpdate/#db.collection.findOneAndUpdate) | ✓                                                            |                                                              |                                                              | ✓                                                            |                                                              |
| [`insert`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert)<br />[`db.collection.insert()`](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#db.collection.insert)<br />[`db.collection.insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)<br />[`db.collection.insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany)<br /> | ✓                                                            |                                                              |                                                              | ✓                                                            |                                                              |
| [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update)<br />[`db.collection.update()`](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update)<br />[`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany)<br />[`db.collection.updateOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne)<br />[`db.collection.replaceOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne) | ✓                                                            |                                                              |                                                              | ✓                                                            |                                                              |

| [3]  | *([1](https://docs.mongodb.com/manual/reference/read-concern/#id3), [2](https://docs.mongodb.com/manual/reference/read-concern/#id7))* Read concern [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") is available only for multi-document transactions, and for transactions, you set the read concern at the transaction level. The operations that support [`"snapshot"`](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot") correspond to the CRUD operations available in transactions. For more information, see [Transactions and Read Concern](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern).<br />*([1](https://docs.mongodb.com/manual/reference/read-concern/#id3), [2](https://docs.mongodb.com/manual/reference/read-concern/#id7))*读关注[“SNAPSHOT”](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot)仅适用于多文档事务，并且对于事务，您可以在事务级别设置读关注。支持[“SNAPSHOT”](https://docs.mongodb.com/manual/reference/read-concern-snapshot/#readconcern."snapshot)的操作对应于事务中可用的CRUD操作。更多相关信息，请参见[事务和读关注](https://docs.mongodb.com/manual/core/transactions/#transactions-read-concern) |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

## Considerations

## 注意事项

### Read Your Own Writes

### 读自己的写

*Changed in version 3.6.*

*在版本3.6中更改*

Starting in MongoDB 3.6, you can use [causally consistent sessions](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions) to read your own writes, if the writes request acknowledgement.

从MongoDB 3.6版本开始，如果写请求确认，你可以使用[因果一致的会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)读你自己写入的内容。

Prior to MongoDB 3.6, you must have issued your write operation with `{ w: "majority" }` write concern and then use either [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") or [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern for the read operations to ensure that a single thread can read its own writes.

在MongoDB 3.6之前，您必须使用 `{ w: "majority" }` 写关注发出写操作，然后对读操作使用 [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") 或者 [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable")读关注，以确保单个线程可以读取自己的写入内容

### Real Time Order

### 实时顺序

Combined with [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern, [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern enables multiple threads to perform reads and writes on a single document as if a single thread performed these operations in real time; that is, the corresponding schedule for these reads and writes is considered linearizable.

结合[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 写关注，[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") 读关注使多个线程可以在单个文档上执行读写操作，就好像单个线程实时地执行了这些操作一样。 也就是说，这些读写的对应的计划被认为是线性的。

### Performance Comparisons

### 性能比较

Unlike [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority"), [`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") read concern confirms with secondary members that the read operation is reading from a primary that is capable of confirming writes with [`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern. [[4\]](https://docs.mongodb.com/manual/reference/read-concern/#edge-cases-2-primaries) As such, reads with linearizable read concern may be significantly slower than reads with [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") or [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local") read concerns.

与[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority")不同，[`"linearizable"`](https://docs.mongodb.com/manual/reference/read-concern-linearizable/#readconcern."linearizable") 的读关注通过从节点确认读操作正在从主节点读，该操作能够以[`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写关注来确认写入。 [[4\]](https://docs.mongodb.com/manual/reference/read-concern/#edge-cases-2-primaries)因此，具有线性化读关注的读取可能比具有[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") 或 [`"local"`](https://docs.mongodb.com/manual/reference/read-concern-local/#readconcern."local")读关注的读慢得多。

Always use `maxTimeMS` with linearizable read concern in case a majority of data bearing members are unavailable. `maxTimeMS` ensures that the operation does not block indefinitely and instead ensures that the operation returns an error if the read concern cannot be fulfilled.

为了避免万一大多数数据承载成员不可用，请始终将 `maxTimeMS` 与可线性化的读确认一起使用。`maxTimeMS` 确保操作不会无限期地阻塞，而是确保如果无法满足读取要求，则操作将返回错误。

For example:

例如：

```
db.restaurants.find( { _id: 5 } ).readConcern("linearizable").maxTimeMS(10000)

db.runCommand( {
     find: "restaurants",
     filter: { _id: 5 },
     readConcern: { level: "linearizable" },
     maxTimeMS: 10000
} )
```

| [[4\]](https://docs.mongodb.com/manual/reference/read-concern/#id8) | In [some circumstances](https://docs.mongodb.com/manual/core/read-preference-use-cases/#edge-cases), two nodes in a replica set may *transiently* believe that they are the primary, but at most, one of them will be able to complete writes with [`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern. The node that can complete [`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a [network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference [`primary`](https://docs.mongodb.com/manual/core/read-preference/#primary), and new writes to the former primary will eventually roll back.<br />在[某些情况](https://docs.mongodb.com/manual/core/read-preference-use-cases/#edge-cases)下，副本集中的两个节点可能会短暂地认为它们是主节点，但至多，其中一个节点将能够以[`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写关注完成。 可以完成[`{ w: "majority" }`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写入的节点是当前主节点，另一个节点是前主节点，由于[网络分区](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition)的原因，该主节点尚未意识到其降级。 发生这种情况时，尽管请求的读优先级为[主节点](https://docs.mongodb.com/manual/core/read-preference/#primary)，但连接到前主节点的客户端仍可能会读到过时的数据，并且最终将对前主节点新写入的进行回滚。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

### Read Operations and `afterClusterTime`

### 读操作和`afterClusterTime`

*New in version 3.6.*

*3.6 版本新加入*

MongoDB 3.6 introduces support for [causally consistent sessions](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions). For read operations associated with causally consistent session, MongoDB 3.6 introduces the `afterClusterTime` read concern option to be set automatically by the drivers for operations associated with causally consistent sessions.

MongoDB 3.6引入了对[因果一致会话](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#sessions)的支持。 对于与因果一致的会话相关联的读操作，MongoDB 3.6引入了 `afterClusterTime` 读关注选项，驱动程序会自动将`afterClusterTime` 读关注选项设置为与因果一致的会话相关联的操作。

IMPORTANT

重要

Do not manually set `afterClusterTime` for a read operation. MongoDB drivers set this value automatically for operations associated with causally consistent sessions. However, you can advance the operation time and the cluster time for the session, such as to be consistent with the operations of another client session. For an example, see [Examples](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency-examples).

不要手动为读操作设置 `afterClusterTime` 。 MongoDB驱动程序会针对与因果一致的会话相关联的操作自动设置此值。 但是，您可以提前会话的操作时间和群集时间，以便与另一个客户端会话的操作保持一致。 有关示例，请参见[示例](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/#causal-consistency-examples)。

To satisfy a read request with an `afterClusterTime` value of `T`, a [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) must perform the request after its oplog reaches time `T`. If its oplog has not reached time `T`, the [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) must wait to service the request.

为了满足 `afterClusterTime` 值为`T`的读请求， [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 必须在其oplog到达时间`T`之后执行请求。如果其oplog尚未达到时间`T`，则 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 必须等待服务该请求。

Read operations with a specified `afterClusterTime` return data that meet both the [read concern level](https://docs.mongodb.com/manual/reference/read-concern/#read-concern-levels) requirement and the specified `afterClusterTime` requirement.

使用指定的 `afterClusterTime` 的读操作将返回满足读关注级别要求和指定的 `afterClusterTime` 要求的数据。

For read operations not associated with causally consistent sessions, `afterClusterTime` is unset.

对于与因果一致会话无关的读操作，未设置 `afterClusterTime`。



原文链接：https://docs.mongodb.com/manual/administration/production-notes/

译者：张琦
