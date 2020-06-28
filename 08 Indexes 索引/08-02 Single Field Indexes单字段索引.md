# Single Field Indexes 单字段索引



On this page

- [Create an Ascending Index on a Single Field](https://docs.mongodb.com/manual/core/index-single/#create-an-ascending-index-on-a-single-field)
- [Create an Index on an Embedded Field](https://docs.mongodb.com/manual/core/index-single/#create-an-index-on-an-embedded-field)
- [Create an Index on Embedded Document](https://docs.mongodb.com/manual/core/index-single/#create-an-index-on-embedded-document)
- [Additional Considerations](https://docs.mongodb.com/manual/core/index-single/#additional-considerations)

在本页面

- [在单个字段上创建升序索引](https://docs.mongodb.com/manual/core/index-single/#create-an-ascending-index-on-a-single-field)
- [在嵌入式字段上创建索引](https://docs.mongodb.com/manual/core/index-single/#create-an-index-on-an-embedded-field)
- [在嵌入式文档上创建索引](https://docs.mongodb.com/manual/core/index-single/#create-an-index-on-embedded-document)
- [其他注意事项](https://docs.mongodb.com/manual/core/index-single/#additional-considerations)



MongoDB provides complete support for indexes on any field in a [collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection) of [documents](https://docs.mongodb.com/manual/reference/glossary/#term-document). By default, all collections have an index on the [_id field](https://docs.mongodb.com/manual/indexes/#index-type-id), and applications and users may add additional indexes to support important queries and operations.

MongoDB为[文档](https://docs.mongodb.com/manual/reference/glossary/#term-document)[集合](https://docs.mongodb.com/manual/reference/glossary/#term-collection)中任何字段提供完整的索引支持 。默认情况下，所有集合在[_id字段](https://docs.mongodb.com/manual/indexes/#index-type-id)上都有索引，应用程序和用户可以添加其他索引来支持重要的查询和操作。

This document describes ascending/descending indexes on a single field.

本文档描述了在单个字段上升序/降序索引。

![Diagram of an index on the ``score`` field (ascending).](https://docs.mongodb.com/manual/_images/index-ascending.bakedsvg.svg)

## Create an Ascending Index on a Single Field

## 在单个字段上创建升序索引[¶](https://docs.mongodb.com/manual/core/index-single/#create-an-ascending-index-on-a-single-field)

Consider a collection named `records` that holds documents that resemble the following sample document:

`records`集合，它包含文档如下：

copy 复制

```
{
  "_id": ObjectId("570c04a4ad233577f97dc459"),
  "score": 1034,
  "location": { state: "NY", city: "New York" }
}
```

The following operation creates an ascending index on the `score` field of the `records` collection:

以下操作在`records`集合的`score` 字段上创建升序索引：

copy 复制

```
db.records.createIndex( { score: 1 } )
```

The value of the field in the index specification describes the kind of index for that field. For example, a value of `1` specifies an index that orders items in ascending order. A value of `-1` specifies an index that orders items in descending order. For additional index types, see [index types](https://docs.mongodb.com/manual/indexes/#index-types).

索引规范中字段的值描述了该字段的索引类型。例如，值`1`表示以升序对项目进行排序的索引。值`-1`指定按降序对项目进行排序的索引。有关其他索引类型，请参见[索引类型](https://docs.mongodb.com/manual/indexes/#index-types)。

The created index will support queries that select on the field `score`, such as the following:

创建的索引将支持在 `score`字段上选择的查询，例如：

copy 复制

```
db.records.find( { score: 2 } )
db.records.find( { score: { $gt: 10 } } )
```



## Create an Index on an Embedded Field在嵌入式字段上创建索引

You can create indexes on fields within embedded documents, just as you can index top-level fields in documents. Indexes on embedded fields differ from [indexes on embedded documents](https://docs.mongodb.com/manual/core/index-single/#index-embedded-documents), which include the full content up to the maximum `index size` of the embedded document in the index. Instead, indexes on embedded fields allow you to use a “dot notation,” to introspect into embedded documents.

您可以在嵌入式文档中的字段上创建索引，就像可以在文档中索引顶级字段一样。嵌入字段上的[索引与嵌入文档上的索引](https://docs.mongodb.com/manual/core/index-single/#index-embedded-documents)不同，后者包含完整内容，直到[索引中嵌入文档](https://docs.mongodb.com/manual/core/index-single/#index-embedded-documents)的最大值。相反，嵌入式字段上的索引允许使用“点符号”来表示嵌入式文档。

Consider a collection named `records` that holds documents that resemble the following sample document:

`records`集合，它包含文档如下：

copy 复制

```
{
  "_id": ObjectId("570c04a4ad233577f97dc459"),
  "score": 1034,
  "location": { state: "NY", city: "New York" }
}
```

The following operation creates an index on the `location.state` field:

以下操作在`location.state` 字段上创建索引：

copy 复制

```
db.records.createIndex( { "location.state": 1 } )
```

The created index will support queries that select on the field `location.state`, such as the following:

创建的索引将支持在字段上选择的查询 `location.state`，例如：

copy 复制

```
db.records.find( { "location.state": "CA" } )
db.records.find( { "location.city": "Albany", "location.state": "NY" } )
```

## Create an Index on Embedded Document在嵌入式文档上创建索引[¶](https://docs.mongodb.com/manual/core/index-single/#create-an-index-on-embedded-document)

You can also create indexes on embedded document as a whole.您还可以在整个嵌入式文档上创建索引。

Consider a collection named `records` that holds documents that resemble the following sample document:

`records`集合，它包含文档如下：

copy 复制

```
{
  "_id": ObjectId("570c04a4ad233577f97dc459"),
  "score": 1034,
  "location": { state: "NY", city: "New York" }
}
```

The `location` field is an embedded document, containing the embedded fields `city` and `state`. The following command creates an index on the `location` field as a whole:

该`location`字段是一个嵌入式文档，包含嵌入式字段 `city`和`state`。以下命令在整个`location` 字段上创建索引：

copy 复制

```
db.records.createIndex( { location: 1 } )
```

The following query can use the index on the `location` field:

以下查询可以使用`location`字段上的索引：

copy 复制

```
db.records.find( { location: { city: "New York", state: "NY" } } )
```

NOTE

Although the query can use the index, the result set does not include the sample document above. When performing equality matches on embedded documents, field order matters and the embedded documents must match exactly. See [Query Embedded Documents](https://docs.mongodb.com/manual/reference/method/db.collection.find/#query-embedded-documents) for more information regarding querying on embedded documents.

注意

尽管查询可以使用索引，但是结果集不包括上面的示例文档。在嵌入式文档执行等值匹配时，字段顺序事项和嵌入式文档必须完全匹配。有关[查询嵌入式文档](https://docs.mongodb.com/manual/reference/method/db.collection.find/#query-embedded-documents)的更多信息，请参见[查询嵌入式](https://docs.mongodb.com/manual/reference/method/db.collection.find/#query-embedded-documents)文档。



## Additional Considerations其他注意事项[¶](https://docs.mongodb.com/manual/core/index-single/#additional-considerations)

Applications may encounter reduced performance during index builds, including limited read/write access to the collection. For more information on the index build process, see [Index Builds on Populated Collections](https://docs.mongodb.com/manual/core/index-creation/#index-operations), including the [Index Builds in Replicated Environments](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build) section.

应用程序在建立索引期间可能会遇到性能下降的情况，包括对集合的有限读/写访问权限。有关索引构建过程的更多信息，请参见 [“填充集合上](https://docs.mongodb.com/manual/core/index-creation/#index-operations)的[索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)，包括“ [复制环境中](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)的 [索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)部分。

Some drivers may specify indexes, using `NumberLong(1)` rather than `1` as the specification. This does not have any affect on the resulting index.

一些驱动程序可能使用`NumberLong(1)`而不是 `1`将规范指定为索引。这对结果索引没有任何影响。



原文链接：https://docs.mongodb.com/manual/core/index-single/

译者：莫薇

update：小芒果