# Indexes索引

On this page**在本页面**



- [Default `_id` Index](https://docs.mongodb.com/manual/indexes/#default-id-index)
- [Create an Index](https://docs.mongodb.com/manual/indexes/#create-an-index)
- [Index Types](https://docs.mongodb.com/manual/indexes/#index-types)
- [Index Properties](https://docs.mongodb.com/manual/indexes/#index-properties)
- [Index Use](https://docs.mongodb.com/manual/indexes/#index-use)
- [Indexes and Collation](https://docs.mongodb.com/manual/indexes/#indexes-and-collation)
- [Covered Queries](https://docs.mongodb.com/manual/indexes/#covered-queries)
- [Index Intersection](https://docs.mongodb.com/manual/indexes/#index-intersection)
- [Restrictions](https://docs.mongodb.com/manual/indexes/#restrictions)
- [Additional Considerations](https://docs.mongodb.com/manual/indexes/#additional-considerations)



- [默认`_id`索引](https://docs.mongodb.com/manual/indexes/#default-id-index)
- [创建索引](https://docs.mongodb.com/manual/indexes/#create-an-index)
- [索引类型](https://docs.mongodb.com/manual/indexes/#index-types)
- [索引属性](https://docs.mongodb.com/manual/indexes/#index-properties)
- [索引使用](https://docs.mongodb.com/manual/indexes/#index-use)
- [索引及排序规则](https://docs.mongodb.com/manual/indexes/#indexes-and-collation)
- [涵盖查询](https://docs.mongodb.com/manual/indexes/#covered-queries)
- [交叉索引](https://docs.mongodb.com/manual/indexes/#index-intersection)
- [限制条件](https://docs.mongodb.com/manual/indexes/#restrictions)
- [其他注意事项](https://docs.mongodb.com/manual/indexes/#additional-considerations)



Indexes support the efficient execution of queries in MongoDB. Without indexes, MongoDB must perform a*collection scan*, i.e. scan every document in a collection, to select those documents that match the query statement. If an appropriate index exists for a query, MongoDB can use the index to limit the number of documents it must inspect.

索引支持在MongoDB中高效执行查询。如果没有索引，MongoDB必须执行*集合扫描*，即扫描*集合中的*每个文档，以选择与查询语句匹配的文档。如果查询存在适当的索引，则MongoDB可以使用该索引来限制它必须检查的文档数。

Indexes are special data structures [[1\]](https://docs.mongodb.com/manual/indexes/#b-tree) that store a small portion of the collection’s data set in an easy to traverse form. The index stores the value of a specific field or set of fields, ordered by the value of the field. The ordering of the index entries supports efficient equality matches and range-based query operations. In addition, MongoDB can return sorted results by using the ordering in the index.

索引是特殊的数据结构[[1\]](https://docs.mongodb.com/manual/indexes/#b-tree)，它以易于遍历的形式存储部分集合数据集。索引存储特定字段或字段集的值，按该字段值排序。索引条目的排序支持高效的等值匹配和基于范围的查询操作。另外，MongoDB可以通过使用索引中的顺序返回排序后的结果。

The following diagram illustrates a query that selects and orders the matching documents using an index:

下图说明了使用索引选择和排序匹配文档的查询方式：

![Diagram of a query that uses an index to select and return sorted results. The index stores ``score`` values in ascending order. MongoDB can traverse the index in either ascending or descending order to return sorted results.](https://docs.mongodb.com/manual/_images/index-for-sort.bakedsvg.svg)



Fundamentally, indexes in MongoDB are similar to indexes in other database systems. MongoDB defines indexes at the [collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection) level and supports indexes on any field or sub-field of the documents in a MongoDB collection.

从根本上讲，MongoDB中的索引与其他数据库系统中的索引类似。MongoDB在[集合](https://docs.mongodb.com/manual/reference/glossary/#term-collection) 级别定义索引，并支持MongoDB集合中文档的任何字段或子字段定义索引。



## Default `_id` Index默认`_id`索引

MongoDB creates a [unique index](https://docs.mongodb.com/manual/core/index-unique/#index-type-unique) on the [_id](https://docs.mongodb.com/manual/core/document/#document-id-field) field during the creation of a collection. The `_id` index prevents clients from inserting two documents with the same value for the `_id` field. You cannot drop this index on the `_id`field.

MongoDB 在在创建集合期间在[_id](https://docs.mongodb.com/manual/core/document/#document-id-field)字段上创建了[唯一索引](https://docs.mongodb.com/manual/core/index-unique/#index-type-unique)。该索引可防止客户端插入两个具有[_id](https://docs.mongodb.com/manual/core/document/#document-id-field)字段值相同的文档。[_id](https://docs.mongodb.com/manual/core/document/#document-id-field)字段上的索引不能删除。

NOTE

In [sharded clusters](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster), if you do *not* use the `_id` field as the [shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key), then your application **must** ensure the uniqueness of the values in the `_id` field to prevent errors. This is most-often done by using a standard auto-generated [ObjectId](https://docs.mongodb.com/manual/reference/glossary/#term-objectid).

注意

在分片[群集中](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)，如果您*不*使用`_id`字段作为分片[键](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)，那么您的应用程序 **必须**确保`_id`字段中值的唯一性以防止出错。通常使用标准的自动生成的[ObjectId来完成](https://docs.mongodb.com/manual/reference/glossary/#term-objectid)。

## Create an Index创建索引



To create an index in the [Mongo Shell](https://docs.mongodb.com/manual/tutorial/getting-started/), use [`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex).

要在[Mongo Shell中](https://docs.mongodb.com/manual/tutorial/getting-started/)创建索引 ，请使用 [`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)。

copy 复制

```
db.collection.createIndex( <key and index type specification>, <options> )
```

The following example creates a single key descending index on the `name` field:

下面的示例在`name`字段上创建单个键降序索引：

copy 复制

```
db.collection.createIndex( { name: -1 } )
```

The [`db.collection.createIndex`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) method only creates an index if an index of the same specification does not already exist.

[`db.collection.createIndex`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)方法仅用于创建索引，且相同定义的索引不存在。

| [[1\]](https://docs.mongodb.com/manual/indexes/#id2) | MongoDB indexes use a B-tree data structure. |
| ---------------------------------------------------- | -------------------------------------------- |
|                                                      |                                              |

| [[1\]](https://docs.mongodb.com/manual/indexes/#id2) | MongoDB索引使用B-树数据结构。 |
| ---------------------------------------------------- | ----------------------------- |
|                                                      |                               |

### Index Names索引名称

The default name for an index is the concatenation of the indexed keys and each key’s direction in the index ( i.e. 1 or -1) using underscores as a separator. For example, an index created on `{ item : 1, quantity: -1 }`has the name `item_1_quantity_-1`.

索引的默认名称是使用下划线作为分隔符的索引键和索引中每个键的方向（即1或-1）的串联。例如，在 `{ item : 1, quantity: -1 }`上创建的索引 的名称为 `item_1_quantity_-1`。

You can create indexes with a custom name, such as one that is more human-readable than the default. For example, consider an application that frequently queries the `products` collection to populate data on existing inventory. The following [`createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) method creates an index on `item` and `quantity` named `query forinventory`:

您可以使用自定义名称创建索引，例如比默认名称更易于理解的名称。举个例子，考虑一个经常查询`products`集合以填充现有清单上的数据的应用程序。以下[`createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) 方法在`item`上创建索引并将`quantity`命名为：`query for inventory`

copy 复制

```
db.products.createIndex(
  { item: 1, quantity: -1 } ,
  { name: "query for inventory" }
)
```

You can view index names using the [`db.collection.getIndexes()`](https://docs.mongodb.com/manual/reference/method/db.collection.getIndexes/#db.collection.getIndexes) method. You cannot rename an index once created. Instead, you must drop and re-create the index with a new name.

您可以使用[`db.collection.getIndexes()`](https://docs.mongodb.com/manual/reference/method/db.collection.getIndexes/#db.collection.getIndexes) 方法查看索引名称。一旦创建索引，您将无法重命名。相反，您必须删除并使用新名称来重新创建索引。

## Index Types索引类型

MongoDB provides a number of different index types to support specific types of data and queries.

MongoDB提供了许多不同的索引类型来支持特定类型的数据和查询。

### Single Field 单字段索引

In addition to the MongoDB-defined `_id` index, MongoDB supports the creation of user-defined ascending/descending indexes on a [single field of a document](https://docs.mongodb.com/manual/core/index-single/).

除了MongoDB定义的`_id`索引外，MongoDB还支持在[文档](https://docs.mongodb.com/manual/core/index-single/)的[单个字段](https://docs.mongodb.com/manual/core/index-single/)上创建用户定义的升序/降序索引。

![Diagram of an index on the ``score`` field (ascending).](https://docs.mongodb.com/manual/_images/index-ascending.bakedsvg.svg)

For a single-field index and sort operations, the sort order (i.e. ascending or descending) of the index key does not matter because MongoDB can traverse the index in either direction.

对于单字段索引和排序操作，索引键的排序顺序（即升序或降序）无关紧要，因为MongoDB可以沿任一方向遍历索引。

See [Single Field Indexes](https://docs.mongodb.com/manual/core/index-single/) and [Sort with a Single Field Index](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/#sort-results-single-field) for more information on single-field indexes.

有关[单字段索引](https://docs.mongodb.com/manual/core/index-single/)的更多信息，请参见[单字段索引](https://docs.mongodb.com/manual/core/index-single/)和[使用](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/#sort-results-single-field)[单字段索引](https://docs.mongodb.com/manual/core/index-single/)[排序](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/#sort-results-single-field)。

### Compound Index复合索引[¶](https://docs.mongodb.com/manual/indexes/#compound-index)

MongoDB also supports user-defined indexes on multiple fields, i.e. [compound indexes](https://docs.mongodb.com/manual/core/index-compound/).

MongoDB还支持多个字段上的用户定义索引，即 [复合索引](https://docs.mongodb.com/manual/core/index-compound/)。

The order of fields listed in a compound index has significance. For instance, if a compound index consists of `{userid: 1, score: -1 }`, the index sorts first by `userid` and then, within each `userid` value, sorts by`score`.

复合索引中列出的字段顺序具很重要。例如，如果复合索引为 `{userid: 1, score: -1 }`，则索引首先按 `userid` 字段进行排序，然后在每个 `userid` 值按`score`字段进行排序。

![Diagram of a compound index on the ``userid`` field (ascending) and the ``score`` field (descending). The index sorts first by the ``userid`` field and then by the ``score`` field.](https://docs.mongodb.com/manual/_images/index-compound-key.bakedsvg.svg)

For compound indexes and sort operations, the sort order (i.e. ascending or descending) of the index keys can determine whether the index can support a sort operation. See [Sort Order](https://docs.mongodb.com/manual/core/index-compound/#index-ascending-and-descending) for more information on the impact of index order on results in compound indexes.

对于复合索引和排序操作，索引键的排序顺序（即升序或降序）可以确定索引是否可以支持排序操作。有关索引顺序对复合索引结果的影响的更多信息，请参见 [排序顺序](https://docs.mongodb.com/manual/core/index-compound/#index-ascending-and-descending)。

See [Compound Indexes](https://docs.mongodb.com/manual/core/index-compound/) and [Sort on Multiple Fields](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/#sort-on-multiple-fields) for more information on compound indexes.

有关[复合索引](https://docs.mongodb.com/manual/core/index-compound/)的更多信息，请参见[复合索引](https://docs.mongodb.com/manual/core/index-compound/)和[在多个字段上排序](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/#sort-on-multiple-fields)。



### Multikey Index多键索引



MongoDB uses [multikey indexes](https://docs.mongodb.com/manual/core/index-multikey/) to index the content stored in arrays. If you index a field that holds an array value, MongoDB creates separate index entries for *every* element of the array. These [multikey indexes](https://docs.mongodb.com/manual/core/index-multikey/) allow queries to select documents that contain arrays by matching on element or elements of the arrays. MongoDB automatically determines whether to create a multikey index if the indexed field contains an array value; you do not need to explicitly specify the multikey type.

MongoDB使用[多键索引](https://docs.mongodb.com/manual/core/index-multikey/)来索引存储在数组中的内容。如果索引字段包含数组值，MongoDB将为数组的*每个*元素创建单独的索引条目。这些[多键索引](https://docs.mongodb.com/manual/core/index-multikey/)允许查询通过匹配数组的一个或多个元素来获取包含数组的文档。如果索引字段包含数组值，MongoDB会自动确定是否创建多键索引。不需要显式指定多键类型。

![addr.zip字段上的多键索引图。 addr字段包含地址文档的数组。 地址文档包含``zip''字段。](https://docs.mongodb.com/manual/_images/index-multikey.bakedsvg.svg)

See [Multikey Indexes](https://docs.mongodb.com/manual/core/index-multikey/) and [Multikey Index Bounds](https://docs.mongodb.com/manual/core/multikey-index-bounds/) for more information on multikey indexes.

更多信息参见[多键索引](https://docs.mongodb.com/manual/core/index-multikey/)和[多键索引界](https://docs.mongodb.com/manual/core/multikey-index-bounds/) 。

### Geospatial Index地理空间索引

To support efficient queries of geospatial coordinate data, MongoDB provides two special indexes: [2d indexes](https://docs.mongodb.com/manual/core/2d/) that uses planar geometry when returning results and [2dsphere indexes](https://docs.mongodb.com/manual/core/2dsphere/) that use spherical geometry to return results.

为了支持对地理空间坐标数据的高效查询，MongoDB提供了两个特殊的索引：返回结果时使用平面几何的[2d索引](https://docs.mongodb.com/manual/core/2d/)和使用球面几何的[2dsphere索引](https://docs.mongodb.com/manual/core/2dsphere/)。

See [2d Index Internals](https://docs.mongodb.com/manual/core/geospatial-indexes/) for a high level introduction to geospatial indexes.

有关地理空间索引的更多介绍，请参见[2d Index Internals](https://docs.mongodb.com/manual/core/geospatial-indexes/)。

### Text Indexes文本索引[¶](https://docs.mongodb.com/manual/indexes/#text-indexes)

MongoDB provides a `text` index type that supports searching for string content in a collection. These text indexes do not store language-specific *stop* words (e.g. “the”, “a”, “or”) and *stem* the words in a collection to only store root words.

MongoDB提供了一种`text`索引类型，该类型支持在集合中搜索字符串内容。这些文本索引不存储特定于语言的*停用*词（例如“ the”，“ a”，“ or”），并且在集合中*词干*仅存储根词。

See [Text Indexes](https://docs.mongodb.com/manual/core/index-text/) for more information on text indexes and search.

有关[文本](https://docs.mongodb.com/manual/core/index-text/)索引和搜索的更多信息，请参见[文本索引](https://docs.mongodb.com/manual/core/index-text/)。

### Hashed Indexes哈希索引[¶](https://docs.mongodb.com/manual/indexes/#hashed-indexes)

To support [hash based sharding](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed-sharding), MongoDB provides a [hashed index](https://docs.mongodb.com/manual/core/index-hashed/) type, which indexes the hash of the value of a field. These indexes have a more random distribution of values along their range, but *only* support equality matches and cannot support range-based queries.

为了支持[基于哈希的分片](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed-sharding)，MongoDB提供了[哈希索引](https://docs.mongodb.com/manual/core/index-hashed/)类型，索引字段值的哈希值。这些索引在其范围内具有更随机的值分布，但*仅* 支持等值匹配，且不支持基于范围的查询。



## Index Properties

## 索引属性[¶](https://docs.mongodb.com/manual/indexes/#index-properties)

### Unique Indexes唯一索引[¶](https://docs.mongodb.com/manual/indexes/#unique-indexes)

The [unique](https://docs.mongodb.com/manual/core/index-unique/) property for an index causes MongoDB to reject duplicate values for the indexed field. Other than the unique constraint, unique indexes are functionally interchangeable with other MongoDB indexes.

索引的[唯一](https://docs.mongodb.com/manual/core/index-unique/)属性导致MongoDB拒绝索引字段的重复值。除了唯一约束之外，唯一索引在功能上可以与其他MongoDB索引互换。

### Partial Indexes部分索引

*New in version 3.2.**3.2版中的新功能。*

[Partial indexes](https://docs.mongodb.com/manual/core/index-partial/) only index the documents in a collection that meet a specified filter expression. By indexing a subset of the documents in a collection, partial indexes have lower storage requirements and reduced performance costs for index creation and maintenance.

[部分索引](https://docs.mongodb.com/manual/core/index-partial/)仅索引集合中符合指定过滤表达式的文档。通过索引集合中文档的子集，部分索引具有较低的存储需求，并降低了索引创建和维护的性能成本。

Partial indexes offer a superset of the functionality of sparse indexes and should be preferred over sparse indexes.

部分索引提供了稀疏索引功能的超集，应优先于稀疏索引。

### Sparse Indexes稀疏索引[¶](https://docs.mongodb.com/manual/indexes/#sparse-indexes)

The [sparse](https://docs.mongodb.com/manual/core/index-sparse/) property of an index ensures that the index only contain entries for documents that have the indexed field. The index skips documents that *do not* have the indexed field.

索引的[稀疏](https://docs.mongodb.com/manual/core/index-sparse/)属性可确保索引仅包含具有索引字段的文档的条目。索引会跳过*没有*索引字段的文档。

You can combine the sparse index option with the unique index option to prevent inserting documents that have duplicate values for the indexed field(s) and skip indexing documents that lack the indexed field(s).

您可以将稀疏索引选项与唯一索引选项结合使用，以防止插入具有字段重复值的文档，并跳过没有索引键的文档。



### TTL Indexes TTL索引

[TTL indexes](https://docs.mongodb.com/manual/core/index-ttl/) are special indexes that MongoDB can use to automatically remove documents from a collection after a certain amount of time. This is ideal for certain types of information like machine generated event data, logs, and session information that only need to persist in a database for a finite amount of time.

[TTL索引](https://docs.mongodb.com/manual/core/index-ttl/)是MongoDB可以在指定时间后自动从集合中删除文档使用的特殊索引。对于某些类型的信息（例如计算机生成的事件数据，日志和会话信息），它们仅需要在数据库中保留有限的时间，这是理想的选择。

See: [Expire Data from Collections by Setting TTL](https://docs.mongodb.com/manual/tutorial/expire-data/) for implementation instructions.

请参阅：[通过设置TTL](https://docs.mongodb.com/manual/tutorial/expire-data/)[过期集合中的数据](https://docs.mongodb.com/manual/tutorial/expire-data/)。



## Index Use索引使用

Indexes can improve the efficiency of read operations. The [Analyze Query Performance](https://docs.mongodb.com/manual/tutorial/analyze-query-plan/) tutorial provides an example of the execution statistics of a query with and without an index.

索引可以提高读操作的效率。 [分析查询性能](https://docs.mongodb.com/manual/tutorial/analyze-query-plan/)教程提供有和没有索引的查询的执行统计的示例。

For information on how MongoDB chooses an index to use, see [query optimizer](https://docs.mongodb.com/manual/core/query-plans/#read-operations-query-optimization).

有关MongoDB如何选择要使用的索引的信息，请参阅[查询优化器](https://docs.mongodb.com/manual/core/query-plans/#read-operations-query-optimization)。



## Indexes and Collation索引和排序规则[¶](https://docs.mongodb.com/manual/indexes/#indexes-and-collation)

*New in version 3.4.**3.4版的新功能。*

[Collation](https://docs.mongodb.com/manual/reference/collation/) allows users to specify language-specific rules for string comparison, such as rules for lettercase and accent marks.

[排序规则](https://docs.mongodb.com/manual/reference/collation/)允许用户为字符串比较指定特定于语言的规则，例如字母和重音标记。

==Mongo Shell

To use an index for string comparisons, an operation must also specify the same collation. That is, an index with a collation cannot support an operation that performs string comparisons on the indexed fields if the operation specifies a different collation.

要将索引用于字符串比较，操作还必须指定相同的排序规则。即，如果操作指定了不同的排序规则，则具有排序规则的索引不能支持对索引字段进行字符串比较的操作。

For example, the collection `myColl` has an index on a string field `category` with the collation locale `"fr"`.

例如，集合`myColl`在字符串字段`category`存在索引，该索引的排序规则locale为`"fr"`，即以法语进行升序排序。

copy 复制

```
db.myColl.createIndex( { category: 1 }, { collation: { locale: "fr" } } )
```

The following query operation, which specifies the same collation as the index, can use the index:

以下查询操作指定与索引相同的排序规则，可以使用索引：

copy 复制

```
db.myColl.find( { category: "cafe" } ).collation( { locale: "fr" } )
```

However, the following query operation, which by default uses the “simple” binary collator, cannot use the index:

但是，以下查询操作（默认情况下使用“简单”二进制排序规则）无法使用索引：

copy 复制

```
db.myColl.find( { category: "cafe" } )
```

For a compound index where the index prefix keys are not strings, arrays, and embedded documents, an operation that specifies a different collation can still use the index to support comparisons on the index prefix keys.

对于索引前缀键不是字符串，数组和嵌入式文档的复合索引，指定不同排序规则仍可以使用索引前缀键的索引进行比较。

For example, the collection `myColl` has a compound index on the numeric fields `score` and `price` and the string field `category`; the index is created with the collation locale `"fr"` for string comparisons:

例如，集合`myColl`在数值字段`score`和`price`及字符串字段`category`上具有复合索引；使用以排序规则locale为 `"fr"`进行字符串比较：

copy 复制

```
db.myColl.createIndex(
   { score: 1, price: 1, category: 1 },
   { collation: { locale: "fr" } } )
```

The following operations, which use `"simple"` binary collation for string comparisons, can use the index:

以下操作使用`"simple"`二进制排序规则进行字符串比较，可以使用索引：

copy 复制

```
db.myColl.find( { score: 5 } ).sort( { price: 1 } )
db.myColl.find( { score: 5, price: { $gt: NumberDecimal( "10" ) } } ).sort( { price: 1 } )
```

The following operation, which uses `"simple"` binary collation for string comparisons on the indexed `category` field, can use the index to fulfill only the `score: 5` portion of the query:

以下操作使用`"simple"`二进制排序规则对索引`category`字段进行字符串比较，可以使用索引仅满足部分查询`score: 5`

copy 复制

```
db.myColl.find( { score: 5, category: "cafe" } )
```

For more information on collation, see the [collation reference page](https://docs.mongodb.com/manual/reference/collation/).

有关排序规则的更多信息，请参见[排序规则详情页面](https://docs.mongodb.com/manual/reference/collation/)。

The following indexes only support simple binary comparison and do not support [collation](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#collation):

以下索引仅支持简单的二进制比较规则，不支持[排序规则](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#collation)：

- [text](https://docs.mongodb.com/manual/core/index-text/) indexes, 文字索引

- [2d](https://docs.mongodb.com/manual/core/2d/) indexes, and [2d](https://docs.mongodb.com/manual/core/2d/)索引，以及

- [geoHaystack](https://docs.mongodb.com/manual/core/geohaystack/) indexes.  [geoHaystack](https://docs.mongodb.com/manual/core/geohaystack/)索引。

  

## Covered Queries覆盖查询[¶](https://docs.mongodb.com/manual/indexes/#covered-queries)

When the query criteria and the [projection](https://docs.mongodb.com/manual/reference/glossary/#term-projection) of a query include *only* the indexed fields, MongoDB returns results directly from the index *without* scanning any documents or bringing documents into memory. These covered queries can be *very* efficient.

当查询标准和查询[投影](https://docs.mongodb.com/manual/reference/glossary/#term-projection) 仅包含索引字段，MongoDB直接从索引返回结果，无需扫描的任何文档或文档加载到内存。这些覆盖的查询可以非常有效。

![仅使用索引来匹配查询条件并返回结果的查询图。 MongoDB无需检查索引之外的数据即可完成查询。](https://docs.mongodb.com/manual/_images/index-for-covered-query.bakedsvg.svg)



For more information on covered queries, see [Covered Query](https://docs.mongodb.com/manual/core/query-optimization/#read-operations-covered-query).

有关覆盖查询的更多信息，请参见 [覆盖查询](https://docs.mongodb.com/manual/core/query-optimization/#read-operations-covered-query)。



## [Index Intersection交叉索引¶](https://docs.mongodb.com/manual/indexes/#index-intersection)

MongoDB can use the [intersection of indexes](https://docs.mongodb.com/manual/core/index-intersection/) to fulfill queries. For queries that specify compound query conditions, if one index can fulfill a part of a query condition, and another index can fulfill another part of the query condition, then MongoDB can use the intersection of the two indexes to fulfill the query. Whether the use of a compound index or the use of an index intersection is more efficient depends on the particular query and the system.

MongoDB可以使用[交叉索引](https://docs.mongodb.com/manual/core/index-intersection/)来完成查询。对于指定复合查询条件的查询，如果一个索引可以满足查询条件的一部分，而另一个索引可以满足查询条件的另一部分，则MongoDB可以使用两个索引的交集来满足查询。使用复合索引还是使用索引交集更有效取决于特定查询和系统。

For details on index intersection, see [Index Intersection](https://docs.mongodb.com/manual/core/index-intersection/).

有关索引交集的详细信息，请参见[索引交集](https://docs.mongodb.com/manual/core/index-intersection/)。



## Restrictions限制

Certain restrictions apply to indexes, such as the length of the index keys or the number of indexes per collection. See [Index Limitations](https://docs.mongodb.com/manual/reference/limits/#index-limitations) for details.

某些限制适用于索引，例如索引键的长度或每个集合的索引数。有关详细信息，请参见[索引限制](https://docs.mongodb.com/manual/reference/limits/#index-limitations)。



## Additional Considerations其他注意事项

Although indexes can improve query performances, indexes also present some operational considerations. See [Operational Considerations for Indexes](https://docs.mongodb.com/manual/core/data-model-operations/#data-model-indexes) for more information.

尽管索引可以提高查询性能，但是索引还提出了一些操作上的注意事项。更多有关信息，请参见[索引的操作注意事项](https://docs.mongodb.com/manual/core/data-model-operations/#data-model-indexes)。

Applications may encounter reduced performance during index builds, including limited read/write access to the collection. For more information on the index build process, see [Index Builds on Populated Collections](https://docs.mongodb.com/manual/core/index-creation/#index-operations), including the [Index Builds in Replicated Environments](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build) section.

应用程序在建立索引期间可能会遇到性能下降的情况，包括对集合的有限读/写访问权限。有关索引构建过程的更多信息，请参见 [“填充集合上](https://docs.mongodb.com/manual/core/index-creation/#index-operations)的[索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)，包括“ [复制环境中](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)的 [索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)部分。

Some drivers may specify indexes, using `NumberLong(1)` rather than `1` as the specification. This does not have any affect on the resulting index.

某些驱动程序可能使用`NumberLong(1)`而不是 `1`指定为索引。这对索引结果没有任何影响。



原文链接：https://docs.mongodb.com/manual/indexes/

译者：莫薇

update：小芒果

