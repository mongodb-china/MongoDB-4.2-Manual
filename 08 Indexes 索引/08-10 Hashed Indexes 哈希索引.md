
# Hashed Indexes[](https://docs.mongodb.com/manual/core/index-hashed/#hashed-indexes "Permalink to this headline")哈希索引

MongoDB Manual (Version 4.2）> Indexes > Hashed Indexes

On this page

-   [Hashing Function](https://docs.mongodb.com/manual/core/index-hashed/#hashing-function哈希函数
-   [Create a Hashed Index](https://docs.mongodb.com/manual/core/index-hashed/#create-a-hashed-index)创建一个哈希索引
-   [Considerations](https://docs.mongodb.com/manual/core/index-hashed/#considerations)注意事项

Hashed indexes maintain entries with hashes of the values of the indexed field.
哈希索引使用字段的哈希值构建索引。

Hashed indexes support  [sharding](https://docs.mongodb.com/manual/sharding/)  using hashed shard keys.  [Hashed based sharding](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed-sharding)  uses a hashed index of a field as the shard key to partition data across your sharded cluster.
MongoDB分片中也支持哈希分片键。在分片集群中，如果数据记录的某字段使用哈希索引，在分片中仅需为该字段创建哈希分片键即可。

Using a hashed shard key to shard a collection results in a more random distribution of data. See  [Hashed Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed-sharding)  for more details.
在分片集群中对一个collection进行哈希分片会导致更多的数据随机分布，了解详情请参见 [Hashed Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed-sharding)  。

## Hashing Function[](https://docs.mongodb.com/manual/core/index-hashed/#hashing-function "Permalink to this headline")  哈希函数

Hashed indexes use a hashing function to compute the hash of the value of the index field.  [[1]](https://docs.mongodb.com/manual/core/index-hashed/#hashvalue)  The hashing function collapses embedded documents and computes the hash for the entire value but does not support multi-key (i.e. arrays) indexes.
哈希索引使用一个散列函数计算索引字段的哈希值。对于是嵌套文档的字段，哈希函数将计算整个嵌套文档的哈希值。在多键索引中不能使用哈希索引。

> TIP
> 提示：
> 
> MongoDB automatically computes the hashes when resolving queries usinghashed indexes. Applications do  **not**  need to compute hashes.
> MongoDB在执行使用到哈希索引的查询时自动计算哈希值，不需要应用程序自己计算查询字段的哈希值。

[[1]](https://docs.mongodb.com/manual/core/index-hashed/#id1)Starting in version 4.0, the  [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo "bin.mongo")  shell provides the method  [`convertShardKeyToHashed()`](https://docs.mongodb.com/manual/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed "convertShardKeyToHashed()"). This method uses the same hashing function as the hashed index and can be used to see what the hashed value would be for a key.
[[1]](https://docs.mongodb.com/manual/core/index-hashed/#id1)从MongoDB 4.0开始，[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo "bin.mongo")  shell 提供  [`convertShardKeyToHashed()`](https://docs.mongodb.com/manual/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed "convertShardKeyToHashed()") 函数。该函数可以用来查看一个键的哈希值。
## Create a Hashed Index[](https://docs.mongodb.com/manual/core/index-hashed/#create-a-hashed-index "Permalink to this headline")创建哈希索引

To create a  [hashed index](https://docs.mongodb.com/manual/core/index-hashed/#index-type-hashed), specify  `hashed`  as the value of the index key, as in the following example:
对记录的指定字段创建哈希索引，命令如下：

    db.collection.createIndex( { _id: "hashed" } )

## Considerations[](https://docs.mongodb.com/manual/core/index-hashed/#considerations "Permalink to this headline") 注意事项

MongoDB supports  `hashed`  indexes of any single field. The hashing function collapses embedded documents and computes the hash for the entire value, but does not support multi-key (i.e. arrays) indexes.
MongoDB支持对任何单一字段创建哈希索引，对于是嵌套文档的字段，哈希函数将计算整个嵌套文档的散列值，(例如:"a":{"b":"xxx","c":"xxx"},对于a字段创建哈希索引，哈希函数将计算整个{"b":"xxx","c":"xxx"}的散列值)，但不支持在多键索引中使用哈希索引(例入：不可对属性是数组的字段创建哈希索引)。

You may not create compound indexes that have  `hashed`  index fields or specify a unique constraint on a  `hashed`  index; however, you can create both a  `hashed`  index and an ascending/descending (i.e. non-hashed) index on the same field: MongoDB will use the scalar index for range queries.
我们不可在复合索引中使用哈希索引，也不可在哈希索引上指定唯一约束。但我们可以对同一字段创建一个哈希索引和非哈希索引(例入:升序/降序类索引)：在进行范围查询时，MongoDB将自动选择升序/降序索引。

### 2^53^  Limit[](https://docs.mongodb.com/manual/core/index-hashed/#limit "Permalink to this headline")  2^53^限制

> WARNING 注意
> 
> MongoDB  `hashed`  indexes truncate floating point numbers to 64-bit integers before hashing. For example, a  `hashed`  index would store the same value for a field that held a value of  `2.3`,  `2.2`, and  `2.9`. To prevent collisions, do not use a  `hashed`  index for floating point numbers that cannot be reliably converted to 64-bit integers (and then back to floating point). MongoDB  `hashed`  indexes do not support floating point values larger than 2^53^.
> MongoDB哈希索引在计算散列之前将浮点数截断为64位整数。例如：对于哈希索引属性，数据内容为`2.3`,  `2.2`, 和  `2.9`的记录对应的哈希列值是相同的。为了避免这种冲突，不建议对无法可靠转换成64位整数的浮点数属性字段创建哈希索引。MongoDB不支持对大于2^53^的浮点值使用哈希索引。

To see what the hashed value would be for a key, see  [`convertShardKeyToHashed()`](https://docs.mongodb.com/manual/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed "convertShardKeyToHashed()").
查看键对应的哈希值请查看[`convertShardKeyToHashed()`](https://docs.mongodb.com/manual/reference/method/convertShardKeyToHashed/#convertShardKeyToHashed "convertShardKeyToHashed()")。

### PowerPC and 2^63^[](https://docs.mongodb.com/manual/core/index-hashed/#powerpc-and-263 "Permalink to this headline") PowerPC 和 2^63^

For  [hashed indexes](https://docs.mongodb.com/manual/core/index-hashed/#), MongoDB 4.2 ensures that the hashed value for the floating point value 2^63^  on PowerPC is consistent with other platforms.
PowerPC和其他处理器中，MongoDB 4.2都保证可以计算64位(8字节)双精度浮点数(-1.79E+308 ~ +1.79E+308)的哈希值。

Although  [hashed indexes](https://docs.mongodb.com/manual/core/index-hashed/#)  on a field that may contain floating point values greater than 2^53^  is an unsupported configuration, clients may still insert documents where the indexed field has the value 2^63^.
虽然大于2^53^的浮点数属性是无法配置的，但是客户端仍然可以插入索引字段值是2^63^的文档。

To list all  `hashed`  indexes for all collections in your deployment, you can use the following operation in the  [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo "bin.mongo")  shell:
查询部署的MongoDB实例中所有数据库中所有collection中的哈希索引，可以在[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo "bin.mongo")  shell中使用如下命令：

    db.adminCommand("listDatabases").databases.forEach(function(d){
       let mdb = db.getSiblingDB(d.name);
       mdb.getCollectionInfos({ type: "collection" }).forEach(function(c){
          let currentCollection = mdb.getCollection(c.name);
          currentCollection.getIndexes().forEach(function(idx){
            let idxValues = Object.values(Object.assign({}, idx.key));
    
            if (idxValues.includes("hashed")) {
              print("Hashed index: " + idx.name + " on " + idx.ns);
              printjson(idx);
            };
          });
       });
    });

To check if the indexed field contains the value 2^63^, run the following operation for the collection and the indexed field:
检查某个collection的索引字段数据内容是2^63^位的值，使用如下命令：
-   If the indexed field type is a scalar and never a document:
-  如果一个collection中的索引字段数据内容仅是数值，不存在任何文档：

   ```
    // substitute the actual collection name for <collection>
    // substitute the actual indexed field name for <indexfield>
    
    db.<collection>.find( { <indexfield>: Math.pow(2,63) } );
   ```
   
-   If the indexed field type is a document (or a scalar), you can run:
-  如果一个collection中的索引字段是文档(或者数值)，可以执行：


 ```
// substitute the actual collection name for <collection>
// substitute the actual indexed field name for <indexfield>

db.<collection>.find({
    $where: function() {
        function findVal(obj, val) {
            if (obj === val)
                return true;

            for (const child in obj) {
                if (findVal(obj[child], val)) {
                    return true;
                }
            }
            return false;
        }
        return findVal(this.<indexfield>, Math.pow(2, 63));
    }
})
 ```

原文链接：[https://docs.mongodb.com/manual/core/index-hashed/#hashed-indexes](https://docs.mongodb.com/manual/core/index-hashed/#hashed-indexes)

译者：程哲欣

