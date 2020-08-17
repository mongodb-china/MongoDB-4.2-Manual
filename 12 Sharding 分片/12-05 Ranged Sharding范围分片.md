# Ranged Sharding 范围分片

Range-based sharding involves dividing data into contiguous ranges determined by the shard key values. In this model, documents with “close” shard key values are likely to be in the same [chunk](https://docs.mongodb.com/v4.2/reference/glossary/#term-chunk) or [shard](https://docs.mongodb.com/v4.2/reference/glossary/#term-shard). This allows for efficient queries where reads target documents within a contiguous range. However, both read and write performance may decrease with poor shard key selection. See [Shard Key Selection](https://docs.mongodb.com/v4.2/core/ranged-sharding/#sharding-ranged-shard-key).<br>基于范围的分片会将数据划分为由片键值确定的连续范围。 在此模型中，具有“接近”片键值的文档可能位于相同的块或分片中。 这允许在连续范围内读取目标文档的高效查询。 但是，如果片键选择不佳，则读取和写入性能均可能降低。 请参阅【片键的选择】。

![Diagram of the shard key value space segmented into smaller ranges or chunks.](https://docs.mongodb.com/v4.2/_images/sharding-range-based.bakedsvg.svg)

Range-based sharding is the default sharding methodology if no other options such as those required for [Hashed Sharding](https://docs.mongodb.com/v4.2/core/hashed-sharding/) or [zones](https://docs.mongodb.com/v4.2/core/zone-sharding/#zone-sharding) are configured.<br>如果未配置任何其他选项（例如`哈希分片`或`区域`所需的其他选项），则基于范围的分片是默认的分片方式。

## Shard Key Selection 片键的选择

Ranged sharding is most efficient when the shard key displays the following traits:<br>当片键呈现出以下特征时，范围分片更高效：

- Large [Shard Key Cardinality](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#shard-key-range) 基数大
- Low [Shard Key Frequency](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#shard-key-frequency) 频率低
- Non-[Monotonically Changing Shard Keys](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#shard-key-monotonic) 非单调变更

The following image illustrates a sharded cluster using the field `X` as the shard key. If the values for `X` have a large range, low frequency, and change at a non-monotonic rate, the distribution of inserts may look similar to the following:<br>下图说明了使用字段`X`作为片键的分片群集。 如果`X`的值具有大取值范围，低频率以及非单调变化的特征，则插入的分布可能类似于下面这样：

![Diagram of good shard key distribution](https://docs.mongodb.com/v4.2/_images/sharded-cluster-ranged-distribution-good.bakedsvg.svg)

## Shard a Collection 对一个集合进行分片

Use the [`sh.shardCollection()`](https://docs.mongodb.com/v4.2/reference/method/sh.shardCollection/#sh.shardCollection) method, specifying the full namespace of the collection and the target [index](https://docs.mongodb.com/v4.2/reference/glossary/#term-index)or [compound index](https://docs.mongodb.com/v4.2/reference/glossary/#term-compound-index) to use as the [shard key](https://docs.mongodb.com/v4.2/reference/glossary/#term-shard-key).<br>使用`sh.shardCollection()`方法，指定集合的完整命名空间以及作为片键的目标索引或复合索引。

```
sh.shardCollection( "database.collection", { <shard key> } )
```

> IMPORTANT 重要
>
> - Once you shard a collection, the selection of the shard key is immutable; i.e. you cannot select a different shard key for that collection.<br>一旦对某个集合进行分片后，片键的选择是不可变的。 也就是说，您不能再为该集合选择其他片键。
> - Starting in MongoDB 4.2, you can update a document’s shard key value unless the shard key field is the immutable `_id` field. For details on updating the shard key, see [Change a Document’s Shard Key Value](https://docs.mongodb.com/v4.2/core/sharding-shard-key/#update-shard-key).Before MongoDB 4.2, a document’s shard key field value is immutable.<br>从MongoDB 4.2开始，除非片键字段是不可变的`_id`字段，否则您可以更新文档的片键值。 有关更新片键的详细信息，请参阅【更改文档的片键值】。在MongoDB 4.2以前的版本，片键是不可变的。

### Shard a Populated Collection 对一个已有数据的集合进行分片

If you shard a populated collection:<br>如果您对一个已经包含数据的集合进行分片操作：

- The sharding operation creates the initial chunk(s) to cover the entire range of the shard key values. The number of chunks created depends on the [configured chunk size](https://docs.mongodb.com/v4.2/core/sharding-data-partitioning/#sharding-chunk-size).<br>分片操作将创建初始数据块，以覆盖片键值的整个范围。 创建的数据块数取决于【配置的数据块大小】。
- After the initial chunk creation, the balancer migrates these initial chunks across the shards as appropriate as well as manages the chunk distribution going forward.<br>在初始数据块创建之后，均衡器会在分片上适当地迁移这些初始数据块，并管理后续的数据块分配。

### Shard an Empty Collection 对一个空集合进行分片

If you shard an empty collection:<br>如果您对一个空集合进行分片操作：

- With no zones and zone ranges specified for the empty or non-existing collection:<br>如果没有为空集合或不存在的集合指定区域和区域范围：

  - The sharding operation creates a single empty chunk to cover the entire range of the shard key values.<br>分片操作将创建一个空块，以覆盖片键值的整个范围。
- After the initial chunk creation, the balancer migrates the initial chunk across the shards as appropriate as well as manages the chunk distribution going forward.<br>在创建初始块之后，平衡器将在块之间适当地迁移初始块，并管理后续的块分配。
  
- With zones and zone ranges specified for the empty or a non-existing collection (Available starting in MongoDB 4.0.3)<br>如果已经为空集合或不存在的集合指定区域和区域范围（从MongoDB4.0.3版本起可用）：

  - The sharding operation creates empty chunks for the defined zone ranges as well as any additional chunks to cover the entire range of the shard key values and performs an initial chunk distribution based on the zone ranges. This initial creation and distribution of chunks allows for faster setup of zoned sharding.<br>分片操作会为定义的区域范围以及覆盖该片键值的整个范围的任何其他块创建空数据块，并根据区域范围执行初始数据块分配。数据块的这种初始创建和分配可以使分片设置更加快速。
- After the initial distribution, the balancer manages the chunk distribution going forward.<br>在初始分配之后，均衡器将管理后续的数据块分配。



SEE ALSO 另请参阅

To learn how to deploy a sharded cluster and implement ranged sharding, see [Deploy a Sharded Cluster](https://docs.mongodb.com/v4.2/tutorial/deploy-shard-cluster/#sharding-procedure-setup).<br>要了解如何部署分片集群和实现范围分片，请参阅【部署分片集群】。



原文链接：https://docs.mongodb.com/v4.2/core/ranged-sharding/

译者：刘翔
