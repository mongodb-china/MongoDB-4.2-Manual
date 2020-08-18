[Sharding](https://docs.mongodb.com/manual/sharding/) > [Data Partitioning with Chunks](https://docs.mongodb.com/manual/core/sharding-data-partitioning/) > Split Chunks in a Sharded Cluster



# Split Chunks in a Sharded Cluster

# 在分片集群中拆分数据块

Normally, MongoDB splits a [chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk) after an insert if the chunk exceeds the maximum [chunk size](https://docs.mongodb.com/manual/core/sharding-data-partitioning/#sharding-chunk-size). However, you may want to split chunks manually if:<br>通常，如果某个数据块超过最大块大小，则MongoDB会在插入后对数据块进行拆分。但是，在以下情况下，您可能需要手动拆分数据块：

- you have a large amount of data in your cluster and very few [chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk), as is the case after deploying a cluster using existing data.<br>您的集群中有大量数据，并且只有很少的数据块，就像使用现有数据部署集群后的情况一样
- you expect to add a large amount of data that would initially reside in a single chunk or shard. For example, you plan to insert a large amount of data with [shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key) values between `300` and `400`, *but* all values of your shard keys are between `250` and `500` are in a single chunk.<br>您希望添加大量最初驻留在单个数据块或分片中的数据。例如，您计划插入大量数据，这些数据的分片键值在`300`到`400`之间，但是所有分片键的值在`250`到`500`之间都在一个块中(且落在一个分片上)。

> NOTE 注意
>
> MongoDB provides the [`mergeChunks`](https://docs.mongodb.com/manual/reference/command/mergeChunks/#dbcmd.mergeChunks) command to combine contiguous chunk ranges into a single chunk. See [Merge Chunks in a Sharded Cluster](https://docs.mongodb.com/manual/tutorial/merge-chunks-in-sharded-cluster/) for more information.<br>MongoDB提供了`mergeChunks`命令以将连续的块范围合并为一个块。有关更多信息，请参考【在分片群集中合并数据块】。

The [balancer](https://docs.mongodb.com/manual/reference/glossary/#term-balancer) may migrate recently split chunks to a new shard immediately if the move benefits future insertions. The balancer does not distinguish between chunks split manually and those split automatically by the system.<br>如果移动有利于接下来的插入，则`平衡器`可以立即将最近拆分的数据块迁移到新的分片上。平衡器不会区分是手动拆分的数据块还是系统自动拆分的数据块。

> WARNING 警告
>
> Be careful when splitting data in a sharded collection to create new chunks. When you shard a collection that has existing data, MongoDB automatically creates chunks to evenly distribute the collection. To split data effectively in a sharded cluster you must consider the number of documents in a chunk and the average document size to create a uniform chunk size. When chunks have irregular sizes, shards may have an equal number of chunks but have very different data sizes. Avoid creating splits that lead to a collection with differently sized chunks.<br>在分片集合中拆分数据以创建新数据块时，请务必小心。当你对一个已有数据的集合进行分片操作时，MongoDB会自动创建数据块以均匀分布该集合。为了有效地在分片群集中拆分数据，必须考虑单个数据块中的文档数和平均文档大小才能创建统一的数据块大小。当数据块的大小不规则时，分片间可能具有相同数量的数据块，但它们的数据大小却大不相同。应避免由于创建时的拆分而导致的分片集合具有大小不同的数据块现象。

Use [`sh.status()`](https://docs.mongodb.com/manual/reference/method/sh.status/#sh.status) to determine the current chunk ranges across the cluster.<br>使用`sh.status()`来确定当前集群中的数据块范围

To split chunks manually, use the [`split`](https://docs.mongodb.com/manual/reference/command/split/#dbcmd.split) command with either fields `middle` or `find`. The [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell provides the helper methods [`sh.splitFind()`](https://docs.mongodb.com/manual/reference/method/sh.splitFind/#sh.splitFind) and [`sh.splitAt()`](https://docs.mongodb.com/manual/reference/method/sh.splitAt/#sh.splitAt).<br>想要手动进行数据块的拆分，使用带`middle`或者`find`字段的`split`命令。mongos shell提供了辅助方法`sh.splitFind()`和`sh.splitAt()`

[`splitFind()`](https://docs.mongodb.com/manual/reference/method/sh.splitFind/#sh.splitFind) splits the chunk that contains the *first* document returned that matches this query into two equally sized chunks. You must specify the full namespace (i.e. “`<databse>.<collection>`”) of the sharded collection to [`splitFind()`](https://docs.mongodb.com/manual/reference/method/sh.splitFind/#sh.splitFind). The query in [`splitFind()`](https://docs.mongodb.com/manual/reference/method/sh.splitFind/#sh.splitFind) does not need to use the shard key, though it nearly always makes sense to do so.<br>`splitFind()`将包含与该查询匹配的返回的*第一个*文档的数据块拆分为两个大小相等的数据块。 您必须将分片集合的完整命名空间（即“` <数据库>.<集合>`”）指定给`splitFind()`。 `splitFind()`中的查询可以不使用分片键，尽管这样做几乎总是有意义的（指可以利用到分片键索引）。

> EXAMPLE 示例
>
> The following command splits the chunk that contains the value of `63109` for the `zipcode` field in the `people` collection of the `records` database:<br>下面的命令对`records`数据库的`people`集合为包含`zipcode`字段值为`63109`的数据块进行拆分：
> ```json
> sh.splitFind( "records.people", { "zipcode": "63109" } )
> ```

Use [`splitAt()`](https://docs.mongodb.com/manual/reference/method/sh.splitAt/#sh.splitAt) to split a chunk in two, using the queried document as the lower bound in the new chunk:
使用[`splitAt()`](https://docs.mongodb.com/manual/reference/method/sh.splitAt/#sh.splitAt)将大块一分为二，将查询的文档用作新的块的下限：

> EXAMPLE 示例
>
> The following command splits the chunk that contains the value of `63109` for the `zipcode` field in the `people` collection of the `records` database.<br>下面的命令对`records`数据库的`people`集合为包含`zipcode`字段值为`63109`的数据块进行拆分：
> ```json
> sh.splitAt( "records.people", { "zipcode": "63109" } )
> ```

> NOTE 注意
>
> [`splitAt()`](https://docs.mongodb.com/manual/reference/method/sh.splitAt/#sh.splitAt) does not necessarily split the chunk into two equally sized chunks. The split occurs at the location of the document matching the query, regardless of where that document is in the chunk.<br>`splitAt()`不一定会将数据块平均分为两个大小相等的块。拆分发生在于查询匹配的文档的位置，而不会考虑该文档在整个数据块中的位置。

SEE ALSO 另请参考

[Empty Collection](https://docs.mongodb.com/manual/core/sharding-data-partitioning/#initial-chunks-empty-collection)<br>【空集合】



原文链接：https://docs.mongodb.com/manual/tutorial/split-chunks-in-sharded-cluster/index.html

译者：刘翔
