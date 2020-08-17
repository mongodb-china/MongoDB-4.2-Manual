# Hashed Sharding 哈希分片

Hashed sharding uses a [hashed index](https://docs.mongodb.com/v4.2/core/index-hashed/#index-hashed-index) to partition data across your shared cluster. Hashed indexes compute the hash value of a single field as the index value; this value is used as your shard key. [[1\]](https://docs.mongodb.com/v4.2/core/hashed-sharding/#hashvalue)<br>哈希分片使用[哈希索引](https://docs.mongodb.com/v4.2/core/index-hashed/#index-hashed-index)来在分片集群中对数据进行划分。哈希索引计算某一个字段的哈希值作为索引值，这个值被用作片键。

![Diagram of the hashed based segmentation.](https://docs.mongodb.com/v4.2/_images/sharding-hash-based.bakedsvg.svg)

Hashed sharding provides more even data distribution across the sharded cluster at the cost of reducing [Targeted Operations vs. Broadcast Operations](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-query-isolation). Post-hash, documents with “close” shard key values are unlikely to be on the same chunk or shard - the [`mongos`](https://docs.mongodb.com/v4.2/reference/program/mongos/#bin.mongos) is more likely to perform [Broadcast Operations](https://docs.mongodb.com/v4.2/core/sharded-cluster-query-router/#sharding-mongos-broadcast) to fulfill a given ranged query. [`mongos`](https://docs.mongodb.com/v4.2/reference/program/mongos/#bin.mongos) can target queries with equality matches to a single shard.<br>哈希分片以减少定向操作和增加广播操作为代价，分片集群内的数据分布更加均衡。在哈希之后，拥有比较“接近”的片键的文档将不太可能会分布在相同的数据库或者分片上。mongos更有可能执行广播操作来完成一个给定的范围查询。相对的，mongos可以将等值匹配的查询直接定位到单个分片上。

> TIP 注意：
>
> MongoDB automatically computes the hashes when resolving queries using hashed indexes. Applications do **not** need to compute hashes.<br>当使用哈希索引来解析查询时，MongoDB会自动计算哈希值。应用程序**不需要**计算哈希。

>  WARNING 警告
>
> MongoDB `hashed` indexes truncate floating point numbers to 64-bit integers before hashing. For example, a `hashed` index would store the same value for a field that held a value of `2.3`, `2.2`, and `2.9`. To prevent collisions, do not use a `hashed` index for floating point numbers that cannot be reliably converted to 64-bit integers (and then back to floating point). MongoDB `hashed` indexes do not support floating point values larger than 2^53.<br>MongoDB哈希索引在哈希计算之前会将浮点数截断为64位整数。 例如，哈希索引会将为具有`2.3`、`2.2`和`2.9`的值的字段存储为相同的值。 为了避免冲突，请勿对不能可靠地转换为64位整数（然后再返回到浮点）的浮点数使用哈希索引。 MongoDB哈希索引不支持大于2^53的浮点值。
>
> To see what the hashed value would be for a key, see [`convertShardKeyToHashed()`](https://docs.mongodb.com/v4.2/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed).<br>
>
> 如果想查看一个键的哈希值是什么，请参考`convertShardKeyToHashed（）`。

| [[1\]](https://docs.mongodb.com/v4.2/core/hashed-sharding/#id1) | Starting in version 4.0, the [`mongo`](https://docs.mongodb.com/v4.2/reference/program/mongo/#bin.mongo) shell provides the method [`convertShardKeyToHashed()`](https://docs.mongodb.com/v4.2/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed). This method uses the same hashing function as the hashed index and can be used to see what the hashed value would be for a key. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              | 从4.0版开始，mongo shell提供了`convertShardKeyToHashed（）`方法。 此方法使用与哈希索引相同的哈希函数，可用于查看键的哈希值。 |

## Hashed Sharding Shard Key  哈希分片的片键

The field you choose as your hashed shard key should have a good [cardinality](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#shard-key-range), or large number of different values. Hashed keys are ideal for shard keys with fields that change [monotonically](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#shard-key-monotonic) like [ObjectId](https://docs.mongodb.com/v4.2/reference/glossary/#term-objectid) values or timestamps. A good example of this is the default `_id` field, assuming it only contains [ObjectID](https://docs.mongodb.com/v4.2/reference/glossary/#term-objectid) values.<br>您选择作为哈希片键的字段应具有良好的【基数】或者该字段包含大量不同的值。 哈希分片非常适合选取具有像`ObjectId`值或时间戳那样单调更改的字段作为片键。 一个很好的例子是默认的`_id`字段，假设它仅包含`ObjectID`值（而非用户自定义的`_id`）。

To shard a collection using a hashed shard key, see [Shard a Collection](https://docs.mongodb.com/v4.2/tutorial/deploy-shard-cluster/#deploy-hashed-sharded-cluster-shard-collection).<br>要使用哈希片键对集合进行分片，请参阅【对集合进行分片】。

## Hashed vs Ranged Sharding 哈希分片 VS 范围分片

Given a collection using a monotonically increasing value `X` as the shard key, using ranged sharding results in a distribution of incoming inserts similar to the following:<br>给定一个使用单调递增的值`X`作为片键的集合，使用范围分片会导致插入数据的分布类似于下面这样：

![Diagram of poor shard key distribution due to monotonically increasing or decreasing shard key](https://docs.mongodb.com/v4.2/_images/sharded-cluster-monotonic-distribution.bakedsvg.svg)



Since the value of `X` is always increasing, the chunk with an upper bound of [maxKey](https://docs.mongodb.com/v4.2/reference/bson-types/) receives the majority incoming writes. This restricts insert operations to the single shard containing this chunk, which reduces or removes the advantage of distributed writes in a sharded cluster.<br>由于`X`的值始终在增加，因此具有`maxKey`(上限)的数据块将接收大多数传入的写操作。 这将插入操作限制在只能定向到包含此块的单个分片，从而减少或消除了分片集群中分布式写入的优势。

By using a hashed index on `X`, the distribution of inserts is similar to the following:<br>通过在`X`上使用哈希索引，插入的分布将类似于下面这样：

![Diagram of hashed shard key distribution](https://docs.mongodb.com/v4.2/_images/sharded-cluster-hashed-distribution.bakedsvg.svg)

Since the data is now distributed more evenly, inserts are efficiently distributed throughout the cluster.<br>由于现在数据分布更加均匀，因此可以在整个集群中更高效地分布式插入数据。

## Shard the Collection 对一个集合进行分片

Use the [`sh.shardCollection()`](https://docs.mongodb.com/v4.2/reference/method/sh.shardCollection/#sh.shardCollection) method, specifying the full namespace of the collection and the target [hashed index](https://docs.mongodb.com/v4.2/core/index-hashed/) to use as the [shard key](https://docs.mongodb.com/v4.2/reference/glossary/#term-shard-key).<br>使用`sh.shardCollection()`方法，指定集合的完整命名空间以及作为片键的目标哈希索引。

```
sh.shardCollection( "database.collection", { <field> : "hashed" } )
```

> IMPORTANT  重要
>
> - Once you shard a collection, the selection of the shard key is immutable; i.e. you cannot select a different shard key for that collection.<br>一旦对某个集合进行分片后，片键的选择是不可变的。 也就是说，您不能再为该集合选择其他的片键。
> - Starting in MongoDB 4.2, you can update a document’s shard key value unless the shard key field is the immutable `_id` field. For details on updating the shard key, see [Change a Document’s Shard Key Value](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#update-shard-key).Before MongoDB 4.2, a document’s shard key field value is immutable.<br>从MongoDB 4.2开始，除非片键字段是不可变的`_id`字段，否则您可以更新文档的片键值。 有关更新片键的详细信息，请参阅【更改文档的片键值】。在MongoDB 4.2以前的版本，片键是不可变的。

### Shard a Populated Collection 对一个已有数据的集合进行分片

If you shard a populated collection using a hashed shard key:<br>如果您使用哈希片键对一个已经包含数据的集合进行分片操作：

- The sharding operation creates the initial chunk(s) to cover the entire range of the shard key values. The number of chunks created depends on the [configured chunk size](https://docs.mongodb.com/v4.2/core/sharding-data-partitioning/#sharding-chunk-size).<br>分片操作将创建初始数据块，以覆盖片键值的整个范围。 创建的数据块数取决于【配置的数据块大小】。
- After the initial chunk creation, the balancer migrates these initial chunks across the shards as appropriate as well as manages the chunk distribution going forward.<br>在初始数据块创建之后，均衡器会在分片上适当地迁移这些初始数据块，并管理后续的数据块分配。

### Shard an Empty Collection 对一个空集合进行分片

If you shard an empty collection using a hashed shard key:<br>如果您使用哈希片键对一个空集合进行分片操作：

- With no zones and zone ranges specified for the empty or non-existing collection:<br>如果没有为空集合或不存在的集合指定区域和区域范围：

  - The sharding operation creates empty chunks to cover the entire range of the shard key values and performs an initial chunk distribution. By default, the operation creates 2 chunks per shard and migrates across the cluster. You can use `numInitialChunks` option to specify a different number of initial chunks. This initial creation and distribution of chunks allows for faster setup of sharding.<br>分片操作将创建空数据块，以覆盖片键值的整个范围，并执行初始数据块分配。默认情况下，该操作为每个分片创建2个数据块，并在整个集群中迁移。您可以使用`numInitialChunks`选项指定不同数量的初始块。数据块的这种初始创建和分配可以使分片设置更加快速。
- After the initial distribution, the balancer manages the chunk distribution going forward.<br>初始分配之后，均衡器将管理后续的数据块分配。
  
- With zones and zone ranges  specified for the empty or a non-existing collection (Available starting in MongoDB 4.0.3),<br>如果已经为空集合或不存在的集合指定区域和区域范围（从MongoDB4.0.3版本起可用）：

  - The sharding operation creates empty chunks for the defined zone ranges as well as any additional chunks to cover the entire range of the shard key values and performs an initial chunk distribution based on the zone ranges. This initial creation and distribution of chunks allows for faster setup of zoned sharding.<br>分片操作会为定义的区域范围以及所有其他分片创建空数据块，以覆盖片键值的整个范围，并根据区域范围执行初始数据块分配。数据块的这种初始创建和分配可以使分片设置更加快速。
- After the initial distribution, the balancer manages the chunk distribution going forward.<br>初始分配之后，均衡器将管理后续的数据块分配。



SEE ALSO 另请参考：

To learn how to deploy a sharded cluster and implement hashed sharding, see [Deploy a Sharded Cluster](https://docs.mongodb.com/v4.2/tutorial/deploy-shard-cluster/#sharding-procedure-setup).<br>要了解如何部署分片集群和实现哈希分片，请参阅【部署分片集群】。



原文链接：https://docs.mongodb.com/v4.2/core/hashed-sharding/

译者：刘翔
