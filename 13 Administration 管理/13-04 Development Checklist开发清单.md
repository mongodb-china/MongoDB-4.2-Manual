# [Development Checklist](https://docs.mongodb.com/manual/administration/production-checklist-development/#development-checklist )

# 开发清单




- [Data Durability]数据持久性(https://docs.mongodb.com/v4.2/administration/production-checklist-development/#data-durability)

- [Schema Design]架构设计(https://docs.mongodb.com/v4.2/administration/production-checklist-development/#schema-design)

- [Replication]复制(https://docs.mongodb.com/v4.2/administration/production-checklist-development/#replication)

- [Sharding]分片(https://docs.mongodb.com/v4.2/administration/production-checklist-development/#sharding)

- [Drivers]驱动(https://docs.mongodb.com/v4.2/administration/production-checklist-development/#drivers)



The following checklist, along with the [Operations Checklist](https://docs.mongodb.com/v4.2/administration/production-checklist-operations/) list, provides recommendations to help you avoid issues in your production MongoDB deployment.                                                                                                       

下面的清单以及[操作清单](https://docs.mongodb.com/v4.2/administration/production-checklist-operations/)列表提供了一些建议，帮助我们在生产环境下，避免MongoDB部署出现中的问题。

## Data Durability 

## 数据持久性

- Ensure that your replica set includes at least three data-bearing nodes with `w:majority` [write concern](https://docs.mongodb.com/v4.2/reference/write-concern/). Three data-bearing nodes are required for replica-set wide data durability.
- 确保您的副本集包含至少三个带有`w:majority` [写关注](https://docs.mongodb.com/v4.2/reference/write-concern/)的数据承载节点。副本集范围内的数据持久性需要三个数据承载节点。
- Ensure that all instances use [journaling](https://docs.mongodb.com/v4.2/core/journaling/).
- 确保所有实例都使用[日志](https://docs.mongodb.com/v4.2/core/journaling/)。

## Schema Design

## 架构设计

Data in MongoDB has a *dynamic schema*. [Collections](https://docs.mongodb.com/v4.2/reference/glossary/#term-collection) do not enforce [document](https://docs.mongodb.com/v4.2/reference/glossary/#term-document) structure. This facilitates iterative development and polymorphism. Nevertheless, collections often hold documents with highly homogeneous structures. See [Data Modeling Concepts](https://docs.mongodb.com/v4.2/core/data-models/) for more information.

MongoDB中的数据有一个*动态设计*。[集合](https://docs.mongodb.com/v4.2/reference/glossary/#term-collection)强制执行[文档](https://docs.mongodb.com/v4.2/reference/glossary/#term-document)结构。这有助于迭代开发和多态性。然而，集合通常保存具有高度同质结构的文档。 有关详细信息，请参阅[数据建模概念](https://docs.mongodb.com/v4.2/core/data-models/)。

- Determine the set of collections that you will need and the indexes required to support your queries. With the exception of the `_id` index, you must create all indexes explicitly: MongoDB does not automatically create any indexes other than `_id`.    
- 确定支持查询所需的集合集和所需的索引。除了`_id` 索引之外，您必须显式地创建所有索引：MongoDB不会自动创建除`_id`之外的任何索引。                                                                                          
- Ensure that your schema design supports your deployment type: if you planning to use [sharded clusters](https://docs.mongodb.com/v4.2/reference/glossary/#term-sharded-cluster) for horizontal scaling, design your schema to include a strong shard key. The shard key affects read and write performance by determining how MongoDB partitions data. See: [Impacts of Shard Keys on Cluster Operations](https://docs.mongodb.com/v4.2/core/sharding-shard-key/) for information about what qualities a shard key should possess. You cannot change the shard key once it is set.
- 确保架构设计支持您的部署类型：如果您计划使用[分片集群](https://docs.mongodb.com/v4.2/reference/glossary/#term-sharded-cluster)进行水平扩展，请设计您的架构以包含一个强健的片键。片键通过确定MongoDB如何划分数据来影响读写性能。请参见：[片键对集群操作的影响](https://docs.mongodb.com/v4.2/core/sharding-shard-key/)以获取有关片键应具有哪些质量的信息。一旦设置了片键，就不能更改它。
- Ensure that your schema design does not rely on indexed arrays that grow in length without bound. Typically, best performance can be achieved when such indexed arrays have fewer than 1000 elements.
- 请确保您的架构设计不依赖长度不受限制的索引数组。通常，当这种索引数组的元素少于1000个时，可以获得最佳性能。

- Consider the document size limits when designing your schema. The [BSON Document Size](https://docs.mongodb.com/v4.2/reference/limits/#BSON-Document-Size) limit is 16MB per document. If you require larger documents, use [GridFS](https://docs.mongodb.com/v4.2/core/gridfs/).
- 设计架构时请考虑文档大小限制。[BSON文档大小](https://docs.mongodb.com/v4.2/reference/limits/#BSON-Document-Size)限制为每个文档16MB。如果需要更大的文档，请使用[GridFS](https://docs.mongodb.com/v4.2/core/gridfs/)。

## Replication

复制

- Use an odd number of voting members to ensure that elections proceed successfully. You can have up to 7 voting members. If you have an even number of voting members, and constraints, such as cost, prohibit adding another secondary to be a voting member, you can add an [arbiter](https://docs.mongodb.com/v4.2/reference/glossary/#term-arbiter) to ensure an odd number of votes. For additional considerations when using an arbiter for a 3-member replica set (P-S-A), see [Replica Set Arbiter](https://docs.mongodb.com/v4.2/core/replica-set-arbiter/).    
- 使用奇数个有投票权的成员来确保选举顺利进行。最多可以有7个有投票权的成员。如果您有偶数个投票成员，并且限制条件（如成本）禁止添加另一个辅助成员作为投票成员，则可以添加[仲裁器](https://docs.mongodb.com/v4.2/reference/glossary/#term-arbiter)以确保票数为奇数。有关对3成员副本集（P-S-a）使用仲裁器时的其他注意事项，请参阅[副本集仲裁器](https://docs.mongodb.com/v4.2/core/replica-set-arbiter/)。                                                                                                                                   

**NOTE**

**注意**

For the following MongoDB versions, `pv1` increases the likelihood of [`w:1`](https://docs.mongodb.com/v4.2/reference/write-concern/#writeconcern.) rollbacks compared to `pv0` (no longer supported in MongoDB 4.0+) for replica sets with arbiters:

对于以下MongoDB版本，对于具有仲裁器的副本集，与`pv0`（MongoDB 4.0+中不再支持）相比， `pv1`增加了 [`w:1`](https://docs.mongodb.com/v4.2/reference/write-concern/#writeconcern.) 回滚的可能性：

- MongoDB 3.4.1
- MongoDB 3.4.1
- MongoDB 3.4.0
- MongoDB 3.4.0
- MongoDB 3.2.11 or earlier
- MongoDB 3.2.11 或者更早的版本

See [Replica Set Protocol Version](https://docs.mongodb.com/v4.2/reference/replica-set-protocol-versions/).

参见[副本集协议版本](https://docs.mongodb.com/v4.2/reference/replica-set-protocol-versions/)。

- Ensure that your secondaries remain up-to-date by using [monitoring tools](https://docs.mongodb.com/v4.2/administration/monitoring/) and by specifying appropriate [write concern](https://docs.mongodb.com/v4.2/reference/write-concern/).
- 通过使用[监视工具](https://docs.mongodb.com/v4.2/administration/monitoring/) 和指定适当的[写入机制,](https://docs.mongodb.com/v4.2/reference/write-concern/)，确保您的辅助文件保持最新。
- Do not use secondary reads to scale overall read throughput. See: [Can I use more replica nodes to scale](http://askasya.com/post/canreplicashelpscaling) for an overview of read scaling. For information about secondary reads, see: [Read Preference](https://docs.mongodb.com/v4.2/core/read-preference/).
- 不要使用辅助读取来扩展总体读吞吐量。请参阅：[是否可以使用更多副本节点进行扩展](http://askasya.com/post/canreplicashelpscaling)，以了解读取扩展的概述。有关辅助读取的信息，请参阅：[读取偏好](https://docs.mongodb.com/v4.2/core/read-preference/) 。

## Sharding 

## 分片

- Ensure that your shard key distributes the load evenly on your shards. See: [Shard Keys](https://docs.mongodb.com/v4.2/core/sharding-shard-key/) for more information.

- 确保片键将负载均匀地分配到分片上。请参见：[片键](https://docs.mongodb.com/v4.2/core/sharding-shard-key/)以获取更多信息。

- Use [targeted operations](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-targeted) for workloads that need to scale with the number of shards.

- 对需要根据切片数量进行扩展的工作负载使用[目标操作](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-targeted)。

- **For MongoDB 3.4 and earlier**, read from the primary nodes for [non-targeted or broadcast](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-broadcast) queries as these queries may be sensitive to [stale or orphaned data](http://blog.mongodb.org/post/74730554385/background-indexing-on-secondaries-and-orphaned).

- **对于MongoDB 3.4和更早版本**，从主节点读取[非目标或广播](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-broadcast)查询，因为这些查询可能对[过时或孤立的数据](http://blog.mongodb.org/post/74730554385/background-indexing-on-secondaries-and-orphaned)敏感。

- **For MongoDB 3.6 and later**, secondaries no longer return orphaned data unless using read concern [available](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available") (which is the default read concern for reads against secondaries when not associated with [causally consistent sessions](https://docs.mongodb.com/v4.2/core/read-isolation-consistency-recency/#sessions)).

- **对于MongoDB 3.6和更高版本**，辅助设备不再返回孤立数据，除非使用[可用的](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available")读策略（这是与[因果一致会话](https://docs.mongodb.com/v4.2/core/read-isolation-consistency-recency/#sessions)不关联时针对辅助设备读取的默认读取策略）。

  Starting in MongoDB 3.6, all members of the shard replica set maintain chunk metadata, allowing them to filter out orphans when not using ["available"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available"). As such, [non-targeted or broadcast](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-broadcast) queries that are not using ["available"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available") can be safely run on any member and will not return orphaned data.

  从 MongoDB 3.6 开始，分片副本集的所有成员都维护块元数据，允许它们在不使用[“可用”](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available")时过滤出孤立的数据。因此，不使用[“可用”](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available")的[非目标或广播](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-broadcast)查询可以安全地在任何成员上运行，并且不会返回孤立的数据。

  The ["available"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available") read concern can return [orphaned documents](https://docs.mongodb.com/v4.2/reference/glossary/#term-orphaned-document) from secondary members since it does not check for updated chunk metadata. However, if the return of orphaned documents is immaterial to an application, the ["available"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available") read concern provides the lowest latency reads possible among the various read concerns.

   ["可用"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available")的读取策略可以从辅助成员返回[孤立文档](https://docs.mongodb.com/v4.2/reference/glossary/#term-orphaned-document)，因为它不检查更新的块元数据。但是如果孤立文档的返回对于应用程序来说无关紧要，那么["可用"](https://docs.mongodb.com/v4.2/reference/read-concern-available/#readconcern."available")的读取策略提供了各种读取关注点中可能的最低延迟读取。

- [Pre-split and manually balance chunks](https://docs.mongodb.com/v4.2/tutorial/create-chunks-in-sharded-cluster/) when inserting large data sets into a new non-hashed sharded collection. Pre-splitting and manually balancing enables the insert load to be distributed among the shards, increasing performance for the initial load.

- 在将大数据集插入新的非哈希分片集合时需要[预分割并手动平衡块](https://docs.mongodb.com/v4.2/tutorial/create-chunks-in-sharded-cluster/)。预分割和手动平衡使插入负载能够在分片之间分布，从而提高初始负载的性能。

## Drivers

## 驱动

- Make use of connection pooling. Most MongoDB drivers support connection pooling. Adjust the connection pool size to suit your use case, beginning at 110-115% of the typical number of concurrent database requests.
- 利用连接池。大多数MongoDB驱动程序支持连接池。调整连接池大小以适合您的用例，从典型并发数据库请求数的110-115%开始。
- Ensure that your applications handle transient write and read errors during replica set elections.
- 请确保您的应用程序在副本集选择期间处理短暂的写入和读取错误。
- Ensure that your applications handle failed requests and retry them if applicable. Drivers **do not** automatically retry failed requests.
- 请确保应用程序处理失败的请求，并在适用的情况下重试。驱动程序**不会**自动重试失败的请求。
- Use exponential backoff logic for database request retries.
- 对数据库请求重试使用指数退避逻辑。
- Use [`cursor.maxTimeMS()`](https://docs.mongodb.com/v4.2/reference/method/cursor.maxTimeMS/#cursor.maxTimeMS) for reads and [wtimeout](https://docs.mongodb.com/v4.2/reference/write-concern/#wc-wtimeout) for writes if you need to cap execution time for database operations.
- 如果需要限制数据库操作的执行时间。使用 [`cursor.maxTimeMS()`](https://docs.mongodb.com/v4.2/reference/method/cursor.maxTimeMS/#cursor.maxTimeMS)读取和 [wtimeout ](https://docs.mongodb.com/v4.2/reference/write-concern/#wc-wtimeout)写入。





原文链接：https://docs.mongodb.com/v4.2/administration/production-checklist-development/

译者：孔令升
