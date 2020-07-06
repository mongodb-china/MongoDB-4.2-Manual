# Write Concern

# 写关注

On this page

在本页中

- [Write Concern Specification](https://docs.mongodb.com/manual/reference/write-concern/#write-concern-specification)
- [Acknowledgment Behavior](https://docs.mongodb.com/manual/reference/write-concern/#acknowledgment-behavior)
- [Calculating Majority for Write Concern](https://docs.mongodb.com/manual/reference/write-concern/#calculating-majority-for-write-concern)
- [写关注规范](https://docs.mongodb.com/manual/reference/write-concern/#write-concern-specification)
- [确认行为](https://docs.mongodb.com/manual/reference/write-concern/#acknowledgment-behavior)
- [为写关注计算 Majority 数量](https://docs.mongodb.com/manual/reference/write-concern/#calculating-majority-for-write-concern)

Write concern describes the level of acknowledgment requested from MongoDB for write operations to a standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) or to [replica sets](https://docs.mongodb.com/manual/replication/) or to [sharded clusters](https://docs.mongodb.com/manual/sharding/). In sharded clusters, [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos) instances will pass the write concern on to the shards.

写关注描述了MongoDB请求的对单机版[mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)或[副本集](https://docs.mongodb.com/manual/replication/) 或 [分片集群](https://docs.mongodb.com/manual/sharding/)的写操作的确认级别。在分片群集中，[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)实例会将写关注传递给分片。

**NOTE：**
For [multi-document transactions](https://docs.mongodb.com/manual/core/transactions/), you set the write concern at the transaction level, not at the individual operation level. Do not explicitly set the write concern for individual write operations in a transaction.

注意
对于 [多文档事务](https://docs.mongodb.com/manual/core/transactions/)，写关注是相对于事务级别而不是单个操作级别。不要为事务中的各个写操作显式设置写关注点。

## Write Concern Specification
## 写关注规范

Write concern can include the following fields:
写关注可以包含以下字段：
```{ w: <value>, j: <boolean>, wtimeout: <number> }```

- the [w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) option to request acknowledgment that the write operation has propagated to a specified number of [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances or to [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances with specified tags.
- 使用[w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w)选项来请求确认写入操作已传播到指定数量的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例或具有指定标签的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例。
- the [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) option to request acknowledgment that the write operation has been written to the on-disk journal.
- [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) 选项，用于请求确认已将写操作写入磁盘日志中
- the [wtimeout](https://docs.mongodb.com/manual/reference/write-concern/#wc-wtimeout) option to specify a time limit to prevent write operations from blocking indefinitely.
- [wtimeout](https://docs.mongodb.com/manual/reference/write-concern/#wc-wtimeout) 选项指定一个时间限制，以防止无限期地阻塞写操作。

### `w` Option
### `w` 选项

The `w` option requests acknowledgment that the write operation has propagated to a specified number of [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances or to [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances with specified tags.

`w` 选项请求确认写入操作已传播到指定数量的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例或具有指定标签的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例。

Using the `w` option, the following `w: ` `<value>` write concerns are available:

使用`w` 选项，可以使用以下`w: ` `<value>`写关注点：

| Value 值                      | Description 描述                                             |
| :---------------------------- | :----------------------------------------------------------- |
| `<number>`                    | Requests acknowledgment that the write operation has propagated to the specified number of [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances. For example:<br />请求确认写操作已传播到指定数量的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例。例如：<br />**w:** **1**<br />Requests acknowledgment that the write operation has propagated to the standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) or the primary in a replica set. `w: 1` is the default write concern for MongoDB. Data can be [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid) if the primary steps down before the write operations have replicated to any of the secondaries.<br />请求确认写操作已传播到单机版[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)或副本集中的主数据库。`w: 1`是MongoDB默认的写关注。如果写操作在复制到其他任何从节点前已下线，则可以[数据回滚](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid)。<br />**w:** **0**<br />Requests no acknowledgment of the write operation. However, `w: 0` may return information about socket exceptions and networking errors to the application. Data can be [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid) if the primary steps down before the write operations have replicated to any of the secondaries.<br />不要求确认写操作。但是，w: 0可能会将有关套接字异常和网络错误的信息返回给应用程序。如果写操作在复制到其他任何从节点前已下线，则可以[数据回滚](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid)。<br />If you specify `w: 0` but include [j: true](https://docs.mongodb.com/manual/reference/write-concern/#wc-j), the [j: true](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) prevails to request acknowledgment from the standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) or the primary of a replica set.<br />如果指定`w: 0`但指定[j: true](https://docs.mongodb.com/manual/reference/write-concern/#wc-j)，则[j: true](https://docs.mongodb.com/manual/reference/write-concern/#wc-j)优先于请求自单机版[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)或副本集主节点的确认。<br />`w` greater than 1 requires acknowledgment from the primary and as many data-bearing secondaries as needed to meet the specified write concern. For example, consider a 3-member replica set with a primary and 2 secondaries. Specifying `w: 2` would require acknowledgment from the primary and one of the secondaries. Specifying `w: 3` would require acknowledgment from the primary and both secondaries.<br />如果`w`大于1，则需要来自主节点和足够多保存有数据的从节点确认，以满足指定的写关注。 例如，对于一个一主两从含有3个成员的副本集来说。 指定`w: 2`将需要主节点和一个从节点的确认。 指定`w: 3`将需要主节点和两个从节点的确认。<br />**NOTE：**<br />[Hidden](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members), [delayed](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members), and [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members) members can acknowledge [`w: <number>`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.) write operations.<br />[隐藏的](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)，[延迟的](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members)和[优先级为0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)的成员都可以确认[`w: <number>`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.)写操作。<br />Delayed secondaries can return write acknowledgment no earlier than the configured [`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay).<br />延迟的从节点可以不早于已配置的[`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay)返回写确认。<br />See [Acknowledgment Behavior](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior) for when [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances acknowledge the write.<br />有关[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例何时确认写入的信息，请参见[确认行为](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior)。 |
| `"majority"`                  | Requests acknowledgment that write operations have propagated to the [calculated majority](https://docs.mongodb.com/manual/reference/write-concern/#calculating-majority-count) of the data-bearing voting members (i.e. primary and secondaries with [`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes) greater than `0`).<br />请求确认写操作已传播到所计算的大多数带有数据的投票成员（即，主节点和从节点的[`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes)大于0）。<br />For example, consider a replica set with 3 voting members, Primary-Secondary-Secondary (P-S-S). For this replica set, [calculated majority](https://docs.mongodb.com/manual/reference/write-concern/#calculating-majority-count) is two, and the write must propagate to the primary and one secondary to acknowledge the write concern to the client.<br />例如，考虑一个具有3个投票成员的副本集，即主-从-从（P-S-S）。对于此副本集，[计算出的多数](https://docs.mongodb.com/manual/reference/write-concern/#calculating-majority-count)为2，并且写入必须传播到主节点和一个从节点，以向客户端确认写关注。<br />**NOTE：**<br />[Hidden](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members), [delayed](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members), and [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members) members with [`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes) greater than `0` can acknowledge[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write operations.<br />[隐藏的](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)，[延迟的](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members)和[优先级为0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)以及[`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes)大于0的成员都可以确认[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写操作。<br />Delayed secondaries can return write acknowledgment no earlier than the configured [`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay).<br />延迟的从节点可以不早于已配置的[`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay)返回写确认。<br />After the write operation returns with a [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") acknowledgment to the client, the client can read the result of that write with a [`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") read Concern.<br />在写操作[`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")确认返回给客户端之后，客户端可以使用[`"majority"`](https://docs.mongodb.com/manual/reference/read-concern-majority/#readconcern."majority") 读关注读取该写操作的结果。<br />See [Acknowledgment Behavior](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior) for when [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances acknowledge the write.<br />有关[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例何时确认写入的信息，请参见[确认行为](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior)。 |
| `<custom write concern name>` | Requests acknowledgment that the write operations have propagated to [`tagged`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].tags) members that satisfy the custom write concern defined in [`settings.getLastErrorModes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.getLastErrorModes). For an example, see [Custom Multi-Datacenter Write Concerns](https://docs.mongodb.com/manual/tutorial/configure-replica-set-tag-sets/#configure-custom-write-concern).<br />请求确认写入操作已传播到满足[`settings.getLastErrorModes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.getLastErrorModes)中定义的自定义写关注[`标记的`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].tags)成员。有关示例，请参阅[自定义多数据中心写关注](https://docs.mongodb.com/manual/tutorial/configure-replica-set-tag-sets/#configure-custom-write-concern)。<br />Data can be [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid) if the custom write concern only requires acknowledgement from the primary and the primary steps down before the write operations have replicated to any of the secondaries.<br />如果自定义的写关注仅要求来自主节点和主节点下线之前写操作已复制到任何从节点的确认，则数据可以[回滚](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#rollback-avoid)。<br />See [Acknowledgment Behavior](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior) for when [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances acknowledge the write.<br />有关[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例何时确认写入的信息，请参见[确认行为](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior)。 |

SEE ALSO 

- [Default MongoDB Read Concerns/Write Concerns](https://docs.mongodb.com/manual/reference/mongodb-defaults/)
- [Replica Set Protocol Version](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/)

也可以看

- [默认的MongoDB 读关注/写关注](https://docs.mongodb.com/manual/reference/mongodb-defaults/)
- [副本集协议版本](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/)

### `j` Option

The `j` option requests acknowledgment from MongoDB that the write operation has been written to the [on-disk journal](https://docs.mongodb.com/manual/core/journaling/).
`j` 选项要求MongoDB确认写操作已写入[磁盘日志](https://docs.mongodb.com/manual/core/journaling/)中。

|      |                                                              |
| ---- | ------------------------------------------------------------ |
| `j`  | If `j: true`, requests acknowledgment that the [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances, as specified in the [w:  <value> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w), have written to the on-disk journal. `j: true` does not by itself guarantee that the write will not be rolled back due to replica set primary failover. <br />如果`j: true`，则请求确认将写入[w:  <value> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w)中指定的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例磁盘日志中。`j: true`本身并不能保证不会因副本集主节点的故障转移而回滚写操作。<br />*Changed in version 3.2:* With [`j: true`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.j), MongoDB returns only after the requested number of members, including the primary, have written to the journal. Previously [`j: true`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.j) write concern in a replica set only requires the [primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary) to write to the journal, regardless of the [w:  <value> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) write concern.<br />*在版本3.2中进行了更改：*使用[`j: true`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.j)，MongoDB仅在包括主节点请求数量的成员写入日志后才返回。以前，副本集中的[`j: true`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.j)写关注仅要求主节点写入日志，而与[w:  <value> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w)写关注无关。 |

**NOTE：**

- Specifying a write concern that includes `j: true` to a [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instance that is running without journaling produces an error.
- If journaling is enabled, [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") may imply `j: true`. The [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) replica set configuration setting determines the behavior. See [Acknowledgment Behavior](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior) for details.
- 给一个不运行日志的 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例指定包含`j: true`的写关注到会产生错误。
- 如果启用日记功能，则[`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")可能意味着`j: true`。  [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) 副本集配置设置确定行为。有关详细信息，请参见[确认行为](https://docs.mongodb.com/manual/reference/write-concern/#wc-ack-behavior)。

### `wtimeout`

### `超时`

This option specifies a time limit, in milliseconds, for the write concern. `wtimeout` is only applicable for `w` values greater than `1`.

此选项为写关注指定了一个以毫秒为单位时间限制。 `wtimeout` 仅适用于`w` 值大于`1`。

`wtimeout` causes write operations to return with an error after the specified limit, even if the required write concern will eventually succeed. When these write operations return, MongoDB **does not** undo successful data modifications performed before the write concern exceeded the `wtimeout` time limit.

`wtimeout` 导致写操作在指定的限制后返回错误，即使最终需要的写关注成功。当这些写操作返回时，MongoDB**不会**撤消在写关注超出`wtimeout` 时间限制之前执行成功的数据修改。

If you do not specify the `wtimeout` option and the level of write concern is unachievable, the write operation will block indefinitely. Specifying a `wtimeout` value of `0` is equivalent to a write concern without the `wtimeout` option.

如果您未指定`wtimeout` 选项，并且无法达到写关注级别，则写操作将无限期阻塞。将`wtimeout` 值指定为`0`等效于没有`wtimeout` 选项的写关注。

## Acknowledgment Behavior

## 确认行为

The [w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) option and the [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) option determine when [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances acknowledge write operations.

[w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) 选项和[j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) 选项确定[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例何时确认写操作。

### Standalone

### 单机版

A standalone [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) acknowledges a write operation either after applying the write in memory or after writing to the on-disk journal. The following table lists the acknowledgment behavior for a standalone and the relevant write concerns:

一个单机版的[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 在应用了内存中的写入之后或写入磁盘日志后，都会确认写操作。下表列出了单机版的确认行为以及相关的写关注：

|                 | `j` is unspecified 未指定 J                                  | `j:true`                      | `j:false`           |
| :-------------- | :----------------------------------------------------------- | :---------------------------- | :------------------ |
| `w: 1`          | In memory <br />内存                                         | On-disk journal<br />硬盘日志 | In memory<br />内存 |
| `w: "majority"` | On-disk journal *if running with journaling*<br />硬盘 *如果运行了日志* | On-disk journal<br />硬盘日志 | In memory<br />内存 |

**NOTE：**

With [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) set to `false`, MongoDB does not wait for [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") writes to be written to the on-disk journal before acknowledging the writes. As such, `majority` write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set.

将[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) 设置为`false`时，MongoDB不会在确认写入之前等待 [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")要写入磁盘的日志。这样，在给定副本集中大多数节点瞬时丢失（例如崩溃和重新启动）的情况下，`majority` 写操作可能会回滚。

### Replica Sets

### 副本集

The value specified to [w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) determines the number of replica set members that must acknowledge the write before returning success. For each eligible replica set member, the [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) option determines whether the member acknowledges writes after applying the write operation in memory or after writing to the on-disk journal.

指定给定的[w](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) 值确定返回成功之前必须确认写入的副本集成员的数量。对于每个合格的副本集成员，[j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) 选项确定成员是在内存中应用写操作之后还是在写入磁盘日志后确认。

- `w: "majority"`

  Any data-bearing voting member of the replica set can contribute to write acknowledgment of [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write operations. 

  复制集的任何带有数据的投票成员都可以对[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 写操作进行写确认。
  The following table lists when the member can acknowledge the write based on the [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) value:
  下表列出了成员何时可以基于[j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) 值确认写入：

  |                               |                                                              |
  | ----------------------------- | ------------------------------------------------------------ |
  | `j` **is unspecified** 未指定 | Acknowledgment depends on the value of [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault):<br />确认取决于[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault)的值：<br />If `true`, acknowledgment requires writing operation to on-disk journal (`j: true`).<br />如果为`true`，则确认需要将操作写入磁盘日志（`j: true`）。<br />[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) defaults to `true` <br />[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) 的值默认为`true`<br />If `false`, acknowledgment requires writing operation in memory (`j: false`).<br />如果为`false`，则确认要求在内存中进行写操作（`j: false`）。 |
  | **j:** **true**               | Acknowledgment requires writing operation to on-disk journal.<br />确认需要将写操作写入磁盘日志。 |
  | **j:** **false**              | Acknowledgment requires writing operation in memory.<br />确认需要在内存中进行写操作 |

  **NOTE：**
  With [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) set to `false`, MongoDB does not wait for [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") writes to be written to the on-disk journal before acknowledging the writes. As such, `majority` write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set.
  将[`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) 设置为`false`时，MongoDB不会在确认写入之前等待 [`w: "majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")要写入磁盘的日志。这样，在给定副本集中大多数节点瞬时丢失（例如崩溃和重新启动）的情况下，`majority` 写操作可能会回滚。

  **NOTE：**
  [Hidden](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members), [delayed](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members), and [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members) members with [`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes) greater than `0` can acknowledge[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write operations.<br />[隐藏的](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)，[延迟的](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members)和[优先级为0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)以及[`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes)大于0的成员都可以确认[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")写操作。<br />Delayed secondaries can return write acknowledgment no earlier than the configured [`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay).<br />延迟的从节点可以不早于已配置的[`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay)返回写确认。<br />

- `w: <number>`

  Any data-bearing member of the replica set can contribute to write acknowledgment of [w: <number> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) write operations.
  
  副本集的任何有数据的成员都可以对[w: <number> ](https://docs.mongodb.com/manual/reference/write-concern/#wc-w) 写操作进行写确认.
  
  The following table lists when the member can acknowledge the write based on the [j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) value:
  
  下表列出了成员何时可以基于[j](https://docs.mongodb.com/manual/reference/write-concern/#wc-j) 值确认写入：
  
  |                                     |                                                              |
  | ----------------------------------- | ------------------------------------------------------------ |
  | `j` **is unspecified** <br />未指定 | Acknowledgment requires writing operation in memory (`j: false`).<br />确认需要在内存中进行写操作（`j: false`）。 |
  | **j:** **true**                     | Acknowledgment requires writing operation to on-disk journal.<br />确认需要将写操作写入磁盘日志。 |
  | **j:** **false**                    | Acknowledgment requires writing operation in memory.<br />确认需要在内存中进行写操作 |

**NOTE：**
[Hidden](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members), [delayed](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members), and [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members) members can acknowledge [`w: <number>`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.) write operations.<br />[隐藏的](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)，[隐藏的](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members)和[优先级为0的](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)的成员可以确认[`w: <number>`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.) 写操作。<br />Delayed secondaries can return write acknowledgment no earlier than the configured [`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay).<br />延迟的从节点可以不早于已配置的[`slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay)返回写确认。<br />

## Calculating Majority for Write Concern

## 计算写关注的"多数"

**TIP**

**提示**

Starting in version 4.2.1, the [`rs.status()`](https://docs.mongodb.com/manual/reference/method/rs.status/#rs.status) returns the [`writeMajorityCount`](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#replSetGetStatus.writeMajorityCount) field which contains the calculated majority number.
从版本4.2.1开始，[`rs.status()`](https://docs.mongodb.com/manual/reference/method/rs.status/#rs.status) 返回[`writeMajorityCount`](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#replSetGetStatus.writeMajorityCount) 字段，其中包含计算出的多数数量。

The majority for write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") is calculated as the smaller of the following values:
写关注[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 是通过以下值中的较小者计算得出的：

- the majority of *all* voting members (including arbiters) vs.
- 所有投票成员（包括仲裁员）中的大多数
- the number of all **data-bearing** voting members.
- 所有**有数据**的投票成员的数量。

WARNING

警告

In cases where the calculated majority number is equal to the number of all **data-bearing** voting members (such as with a 3-member Primary-Secondary-Arbiter deployment), write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") may time out or never be acknowledged if a data bearing voting member is down or unreachable. If possible, use a data-bearing voting member instead of an arbiter.

如果计算出的多数数量等于所有**带有数据**的投票成员的人数（例如，由3个成员组成的主-从-仲裁的部署方式），则写关注[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")可能会超时，或者如果带有数据的投票节点已关闭或无法访问。 如果可能，请使用带有数据的投票节点而不是仲裁节点。

For example, consider:

例如：

- A replica set with 3 voting members, Primary-Secondary-Secondary (P-S-S):

  - The majority of all voting members is 2.
  - The number of all data-bearing voting members is 3.

  The calculated majority is 2, the minimum of 2 and 3. The write must propagate to the primary and one of the secondaries to acknowledge the write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") to the client.

- 具有3个投票成员的副本集，主-从-从（P-S-S）：

  - 所有投票成员中多数数量为2。
  - 所有有数据投票的成员人数为3。

  计算得出的多数数量为2，最小值为2和3。则写必须传播到主节点和任一从节点，以向客户端确认写关注[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 完成。

- A replica set with 3 voting members, Primary-Secondary-Arbiter (P-S-A)

  - The majority of all voting members is 2.
  - The number of all data-bearing voting members is 2.

  The calculated majority is 2, the minimum of 2 and 2. Since the write can only be applied to data-bearing members, the write must propagate to the primary and the secondary to acknowledge the write concern [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") to the client.

- 副本集包含3个投票成员，主-从-仲裁（P-S-A）

  - 所有投票成员中大多数为2。
  - 所有有数据投票的成员人数为2。

  计算得出的多数数量为2，最小值为2和2。由于写只能应用于保存数据的节点，因此该写入必须传播到主节点和从节点，以向客户端确认写关注[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 完成。

TIP

提示

Avoid using a [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern with a (P-S-A) or other topologies that require all data-bearing voting members to be available to acknowledge the writes. Customers who want the durability guarantees of using a [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern should instead deploy a topology that does not require all data bearing voting members to be available (e.g. P-S-S).

避免对（P-S-A）或其他要求所有保存数据的投票成员都可以确认写入的拓扑结构使用 [`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") 写。使用者想要保证在使用[`"majority"`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")读关注时的可靠性，则应该部署一个不要求所有保存数据的投票成员都可以确认写入的拓扑结构（例如P-S-S）。 
