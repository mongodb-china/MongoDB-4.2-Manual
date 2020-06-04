# Bulk Write Operations

# 批量写操作

> On this page 本页面中
>
> - [Overview 总览](https://docs.mongodb.com/manual/core/bulk-write-operations/#overview)
> - [Ordered vs Unordered Operations 有序 VS 无序操作](https://docs.mongodb.com/manual/core/bulk-write-operations/#ordered-vs-unordered-operations)
> - [bulkWrite() Methods bulkWrite()方法](https://docs.mongodb.com/manual/core/bulk-write-operations/#bulkwrite-methods)
> - [Strategies for Bulk Inserts to a Sharded Collection 分片集合的批量插入策略](https://docs.mongodb.com/manual/core/bulk-write-operations/#strategies-for-bulk-inserts-to-a-sharded-collection)

## Overview

MongoDB provides clients the ability to perform write operations in bulk. Bulk write operations affect a *single* collection. MongoDB allows applications to determine the acceptable level of acknowledgement required for bulk write operations.<br>MongoDB使客户端能够批量执行写操作。 批量写入操作会影响*单个*集合。 MongoDB允许应用程序确定批量写入操作所需的可接受的确认级别。

*New in version 3.2.  3.2版本新增* 

The [`db.collection.bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) method provides the ability to perform bulk insert, update, and remove operations. MongoDB also supports bulk insert through the [`db.collection.insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany).<br>`db.collection.bulkWrite()`方法提供了执行批量插入，更新和删除操作的能力。对于批量插入而言，MongoDB也支持`db.collection.insertMany()`。

## Ordered vs Unordered Operations 有序 VS 无序操作

Bulk write operations can be either *ordered* or *unordered*.<br>批量写操作可以是*有序的*，也可以*无序的*。

With an ordered list of operations, MongoDB executes the operations serially. If an error occurs during the processing of one of the write operations, MongoDB will return without processing any remaining write operations in the list. See [ordered Bulk Write](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-example-bulk-write-operation)<br>使用操作的有序列表，MongoDB串行地执行操作。 如果在某个单独的写操作的处理过程中发生错误，MongoDB将直接返回而不再继续处理列表中任何剩余的写操作。 请参考【有序的批量写入】

With an unordered list of operations, MongoDB can execute the operations in parallel, but this behavior is not guaranteed. If an error occurs during the processing of one of the write operations, MongoDB will continue to process remaining write operations in the list. See [Unordered Bulk Write](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-example-unordered-bulk-write).<br>使用无序的操作列表，MongoDB可以并行地执行操作，但是不能保证此行为。 如果某个单独的写操作的处理过程中发生错误，MongoDB将继续处理列表中剩余的写操作。 请参考【无序的批量写入】。

Executing an ordered list of operations on a sharded collection will generally be slower than executing an unordered list since with an ordered list, each operation must wait for the previous operation to finish.<br>在分片集合上执行有序的批量写操作通常比执行无序批量写操作要慢。这是因为对于有序列表而言，每个操作都必须等待上一个操作完成后才能执行。

By default, [`bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) performs `ordered` operations. To specify `unordered` write operations, set `ordered: false` in the options document.

默认情况下，`bulkWrite()`执行**有序的**写入。 要指定**无序的**写入，请在选项文档中设置`ordered：false`。

See [Execution of Operations](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-executionofoperations)<br>请参考【操作的执行】

## bulkWrite() Methods bulkWrite()方法

[`bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) supports the following write operations:<br>`bulkWrite()`支持如下操作：

- [insertOne](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-insertone)
- [updateOne](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-updateonemany)
- [updateMany](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-updateonemany)
- [replaceOne](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-replaceone)
- [deleteOne](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-deleteonemany)
- [deleteMany](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-write-operations-deleteonemany)

Each write operation is passed to [`bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) as a document in an array.<br>每个写操作都以数组中的文档形式被传递给`bulkWrite()`

For example, the following performs multiple write operations:<br>比如说，要执行下面这一系列写操作：

The `characters` collection contains the following documents:<br>`characters`集合包含以下文档：

```json
{ "_id" : 1, "char" : "Brisbane", "class" : "monk", "lvl" : 4 },
{ "_id" : 2, "char" : "Eldon", "class" : "alchemist", "lvl" : 3 },
{ "_id" : 3, "char" : "Meldane", "class" : "ranger", "lvl" : 3 }
```

The following [`bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) performs multiple operations on the collection:<br>接下来的`bulkWrite()`将在此集合上执行批量写入的操作。

```json
try {
   db.characters.bulkWrite(
      [
         { insertOne :
            {
               "document" :
               {
                  "_id" : 4, "char" : "Dithras", "class" : "barbarian", "lvl" : 4
               }
            }
         },
         { insertOne :
            {
               "document" :
               {
                  "_id" : 5, "char" : "Taeln", "class" : "fighter", "lvl" : 3
               }
            }
         },
         { updateOne :
            {
               "filter" : { "char" : "Eldon" },
               "update" : { $set : { "status" : "Critical Injury" } }
            }
         },
         { deleteOne :
            { "filter" : { "char" : "Brisbane"} }
         },
         { replaceOne :
            {
               "filter" : { "char" : "Meldane" },
               "replacement" : { "char" : "Tanys", "class" : "oracle", "lvl" : 4 }
            }
         }
      ]
   );
}
catch (e) {
   print(e);
}
```

The operation returns the following:<br>该操作将返回如下的结果：

```json
{
   "acknowledged" : true,
   "deletedCount" : 1,
   "insertedCount" : 2,
   "matchedCount" : 2,
   "upsertedCount" : 0,
   "insertedIds" : {
      "0" : 4,
      "1" : 5
   },
   "upsertedIds" : {

   }
}
```

For more examples, see [bulkWrite() Examples](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#bulkwrite-example-bulk-write-operation)<br>想了解更多例子，请参考【`bulkWrite()` 示例】

## Strategies for Bulk Inserts to a Sharded Collection 分片集合的批量插入策略

Large bulk insert operations, including initial data inserts or routine data import, can affect [sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)performance. For bulk inserts, consider the following strategies:<br>大量的插入操作（包括初始数据插入或者定时的数据导入）可能会影响分片集群的性能。对于批量插入，请考虑以下策略：

### Pre-Split the Collection 对分片集合进行预拆分

If the sharded collection is empty, then the collection has only one initial [chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk), which resides on a single shard. MongoDB must then take time to receive data, create splits, and distribute the split chunks to the available shards. To avoid this performance cost, you can pre-split the collection, as described in [Split Chunks in a Sharded Cluster](https://docs.mongodb.com/manual/tutorial/split-chunks-in-sharded-cluster/).<br>如果分片集合为空，则该集合只有一个存储在单个分片上的初始数据块，MongoDB必须花一些时间来接收数据，创建拆分并将拆分的块分发到其他分片上。为了避免这种性能开销，您可以对分片集合进行预拆分，请参考【分片集群中的数据块拆分】中的描述。

### Unordered Writes to `mongos` 对mongos的无序写入

To improve write performance to sharded clusters, use [`bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite) with the optional parameter `ordered` set to `false`. [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos) can attempt to send the writes to multiple shards simultaneously. For *empty* collections, first pre-split the collection as described in [Split Chunks in a Sharded Cluster](https://docs.mongodb.com/manual/tutorial/split-chunks-in-sharded-cluster/).<br>

要提高对分片集群的写入性能，请使用带有可选参数`ordered:false`的`bulkWrite()`方法。mongos会尝试同时将写入发送到多个分片。对于空集合，请首先按照【在分片集群中拆分数据块】中描述的进行集合的预拆分。

### Avoid Monotonic Throttling 避免单调插入带来的瓶颈

If your shard key increases monotonically during an insert, then all inserted data goes to the last chunk in the collection, which will always end up on a single shard. Therefore, the insert capacity of the cluster will never exceed the insert capacity of that single shard.<br>如果您的分片键在插入过程中是单调增加的，那么所有插入的数据都会插入到该分片集合的最后一个数据块中，也就是说会落到某单个分片上。因此，集群的插入能力将永远不会超过该单个分片的插入性能（木桶的短板原理）。

If your insert volume is larger than what a single shard can process, and if you cannot avoid a monotonically increasing shard key, then consider the following modifications to your application:<br>如果插入量大于单个分片可以处理的数据量，并且无法避免单调增加的分片键，那么可以考虑对应用程序进行如下修改：

- Reverse the binary bits of the shard key. This preserves the information and avoids correlating insertion order with increasing sequence of values.<br>翻转分片键的二进制位。这样可以保留信息的同时避免插入顺序与递增插入值之间的关联性。
- Swap the first and last 16-bit words to “shuffle” the inserts.<br>交换第一个和最后16比特来实现“随机”插入。

EXAMPLE 示例

The following example, in C++, swaps the leading and trailing 16-bit word of [BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson) [ObjectIds](https://docs.mongodb.com/manual/reference/glossary/#term-objectid) generated so they are no longer monotonically increasing.<br>下面的C++例子中，交换了生成BSON 对象id(`ObjectIds`)的前16和末尾16比特值，因此它不再是单调递增的。



```json
using namespace mongo;
OID make_an_id() {
  OID x = OID::gen();
  const unsigned char *p = x.getData();
  swap( (unsigned short&) p[0], (unsigned short&) p[10] );
  return x;
}

void foo() {
  // create an object
  BSONObj o = BSON( "_id" << make_an_id() << "x" << 3 << "name" << "jane" );
  // now we may insert o into a sharded collection
}
```

SEE ALSO  另请参考

[Shard Keys](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key) for information on choosing a sharded key. Also see [Shard Key Internals](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-internals-shard-keys) (in particular, [Choosing a Shard Key](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-internals-operations-and-reliability)).<br>

【分片键】来获得如何选择分片键的相关信息。另请参考【分片键】（尤其是其中【选择一个分片键】的相关章节）

原文链接：https://docs.mongodb.com/manual/core/bulk-write-operations/

译者：刘翔
