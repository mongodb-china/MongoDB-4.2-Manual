# Compound Indexes

# 复合索引[¶](https://docs.mongodb.com/manual/core/index-compound/#compound-indexes)

On this page

- [Create a Compound Index](https://docs.mongodb.com/manual/core/index-compound/#create-a-compound-index)
- [Sort Order](https://docs.mongodb.com/manual/core/index-compound/#sort-order)
- [Prefixes](https://docs.mongodb.com/manual/core/index-compound/#prefixes)
- [Index Intersection](https://docs.mongodb.com/manual/core/index-compound/#index-intersection)
- [Additional Considerations](https://docs.mongodb.com/manual/core/index-compound/#additional-considerations)

在本页面

- [创建复合索引](https://docs.mongodb.com/manual/core/index-compound/#create-a-compound-index)
- [排序顺序](https://docs.mongodb.com/manual/core/index-compound/#sort-order)
- [前缀](https://docs.mongodb.com/manual/core/index-compound/#prefixes)
- [索引交集](https://docs.mongodb.com/manual/core/index-compound/#index-intersection)
- [其他注意事项](https://docs.mongodb.com/manual/core/index-compound/#additional-considerations)

MongoDB supports *compound indexes*, where a single index structure holds references to multiple fields [[1\]](https://docs.mongodb.com/manual/core/index-compound/#compound-index-field-limit) within a collection’s documents. The following diagram illustrates an example of a compound index on two fields:

MongoDB支持*复合索引*，其中单个索引对集合文档中多个字段[[1\]的](https://docs.mongodb.com/manual/core/index-compound/#compound-index-field-limit)引用。下图说明了两个字段上的复合索引示例：

![Diagram of a compound index on the ``userid`` field (ascending) and the ``score`` field (descending). The index sorts first by the ``userid`` field and then by the ``score`` field.](https://docs.mongodb.com/manual/_images/index-compound-key.bakedsvg.svg)

| [[1\]](https://docs.mongodb.com/manual/core/index-compound/#id1) | MongoDB imposes a [`limit of 32 fields for any compound index`](https://docs.mongodb.com/manual/reference/limits/#Number-of-Indexed-Fields-in-a-Compound-Index). |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |



| [[1\]](https://docs.mongodb.com/manual/core/index-compound/#id1) | 对于复合索引最多支持32个字段。 |
| ------------------------------------------------------------ | ------------------------------ |
|                                                              |                                |

Compound indexes can support queries that match on multiple fields.

复合索引可以支持在多个字段上匹配的查询。



## Create a Compound Index 创建复合索引

To create a [compound index](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound) use an operation that resembles the following prototype:

创建[复合索引，](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)使用如下操作：

copy 复制

```
db.collection.createIndex( { <field1>: <type>, <field2>: <type2>, ... } )
```

The value of the field in the index specification describes the kind of index for that field. For example, a value of `1` specifies an index that orders items in ascending order. A value of `-1` specifies an index that orders items in descending order. For additional index types, see [index types](https://docs.mongodb.com/manual/indexes/#index-types).

索引规范中字段的值描述了该字段的索引类型。例如，值`1`表示以升序对项目进行排序的索引。值`-1`指定按降序对项目进行排序的索引。有关其他索引类型，请参见[索引类型](https://docs.mongodb.com/manual/indexes/#index-types)。

IMPORTANT

You may not create compound indexes that have `hashed` index type. You will receive an error if you attempt to create a compound index that includes [a hashed index field](https://docs.mongodb.com/manual/core/index-hashed/).

重要

您无法创建具有`hashed`索引类型的复合索引 。如果尝试创建包含[哈希索引字段](https://docs.mongodb.com/manual/core/index-hashed/)的复合索引，则会收到错误消息。

Consider a collection named `products` that holds documents that resemble the following document:

如：`products`集合，包含如下文档：

copy 复制

```
{
 "_id": ObjectId(...),
 "item": "Banana",
 "category": ["food", "produce", "grocery"],
 "location": "4th Street Store",
 "stock": 4,
 "type": "cases"
}
```

The following operation creates an ascending index on the `item` and `stock` fields:

以下操作在`item`和 `stock`字段上创建一个升序索引：

copy 复制

```
db.products.createIndex( { "item": 1, "stock": 1 } )
```

The order of the fields listed in a compound index is important. The index will contain references to documents sorted first by the values of the `item` field and, within each value of the `item` field, sorted by values of the stock field. See [Sort Order](https://docs.mongodb.com/manual/core/index-compound/#index-ascending-and-descending) for more information.

复合索引中列出的字段的顺序很重要。索引将包含对文档的引用，这些文档首先按`item`字段的值排序，然后在该`item`字段的每个值按stock字段值进行排序。有关更多信息，请参见[排序顺序](https://docs.mongodb.com/manual/core/index-compound/#index-ascending-and-descending)。

In addition to supporting queries that match on all the index fields, compound indexes can support queries that match on the prefix of the index fields. That is, the index supports queries on the `item` field as well as both `item` and `stock` fields:

除了支持在所有索引字段上匹配的查询之外，复合索引还可以支持在索引字段的前缀上匹配的查询。也就是说，索引支持对`item`和`stock`字段的查询：

copy 复制

```
db.products.find( { item: "Banana" } )
db.products.find( { item: "Banana", stock: { $gt: 5 } } )
```

For details, see [Prefixes](https://docs.mongodb.com/manual/core/index-compound/#compound-index-prefix).

有关详细信息，请参见[前缀](https://docs.mongodb.com/manual/core/index-compound/#compound-index-prefix)。

## Sort Order排序顺序[¶](https://docs.mongodb.com/manual/core/index-compound/#sort-order)

Indexes store references to fields in either ascending (`1`) or descending (`-1`) sort order. For single-field indexes, the sort order of keys doesn’t matter because MongoDB can traverse the index in either direction. However, for [compound indexes](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound), sort order can matter in determining whether the index can support a sort operation.

索引以升序（`1`）或降序（`-1`）排序顺序存储对字段的引用。对于单字段索引，键的排序顺序无关紧要，因为MongoDB可以在任一方向上遍历索引。但是，对于[复合索引](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)，排序顺序决定索引是否支持排序操作。

Consider a collection `events` that contains documents with the fields `username` and `date`. Applications can issue queries that return results sorted first by ascending `username` values and then by descending (i.e. more recent to last) `date` values, such as:

`events`集合包含字段`username`和`date`。应用程序可以发出返回结果的查询，这些查询首先是按升序`username`排序，然后按降序（即从最新到最后）`date`值排序，例如：

copy 复制

```
db.events.find().sort( { username: 1, date: -1 } )
```

or queries that return results sorted first by descending `username` values and then by ascending `date` values, such as:

或返回结果的查询首先按降序`username`值排列 ，然后按升序`date`值排列，例如：

copy 复制

```
db.events.find().sort( { username: -1, date: 1 } )
```

The following index can support both these sort operations:

以下索引可以支持这两种排序操作：

copy 复制

```
db.events.createIndex( { "username" : 1, "date" : -1 } )
```

However, the above index **cannot** support sorting by ascending `username` values and then by ascending `date` values, such as the following:

但是，上面的索引不支持按升序`username`值排序，然后按升序 `date`值排序，如下所示：

copy 复制

```
db.events.find().sort( { username: 1, date: 1 } )
```

For more information on sort order and compound indexes, see [Use Indexes to Sort Query Results](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/).

有关排序顺序和复合索引的更多信息，请参见 [使用索引对查询结果进行排序](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/)。



## Prefixes前缀[¶](https://docs.mongodb.com/manual/core/index-compound/#prefixes)

Index prefixes are the *beginning* subsets of indexed fields. For example, consider the following compound index:

索引前缀是索引字段的*beginning*子集。例如，复合索引：

copy 复制

```
{ "item": 1, "location": 1, "stock": 1 }
```

The index has the following index prefixes:

索引具有以下索引前缀：

- `{ item: 1 }`
- `{ item: 1, location: 1 }`

For a compound index, MongoDB can use the index to support queries on the index prefixes. As such, MongoDB can use the index for queries on the following fields:

对于复合索引，MongoDB可以使用索引来支持对索引前缀的查询。这样，MongoDB可以将索引用于以下字段的查询：

- the `item` field,
- the `item` field *and* the `location` field,
- the `item` field *and* the `location` field *and* the `stock` field.



- `item` 字段,
- `item` 字段和`location`字段,
- `item` 字段，`location`字段和`stock` 字段。



MongoDB can also use the index to support a query on `item` and `stock` fields since `item` field corresponds to a prefix. However, the index would not be as efficient in supporting the query as would be an index on only `item` and `stock`.

MongoDB索引支持对`item`和 `stock`字段的查询，因为`item`字段对应于前缀。但是，索引在支持查询方面的效率不如仅索引`item`和`stock`。

However, MongoDB cannot use the index to support queries that include the following fields since without the `item` field, none of the listed fields correspond to a prefix index:

但是，MongoDB无法使用索引来支持包含如下查询，因为没有`item`字段，所列出的字段无法使用前缀索引：

- the `location` field,
- the `stock` field, or
- the `location` and `stock` fields.



- `location` 字段,
- `stock` 字段，
- `location` 和 `stock`字段。



If you have a collection that has both a compound index and an index on its prefix (e.g. `{ a: 1, b: 1 }` and `{ a: 1 }`), if neither index has a sparse or unique constraint, then you can remove the index on the prefix (e.g. `{ a: 1 }`). MongoDB will use the compound index in all of the situations that it would have used the prefix index.

如果您的集合同时具有复合索引`{ a: 1, b: 1 }`和前缀索引`{ a: 1 }`，且两个索引都没有稀疏或唯一约束，则可以删除前缀上的索引`{ a: 1 }`。MongoDB在所有使用前缀索引的情况下都将使用复合索引。



## Index Intersection索引交集

Starting in version 2.6, MongoDB can use [index intersection](https://docs.mongodb.com/manual/core/index-intersection/) to fulfill queries. The choice between creating compound indexes that support your queries or relying on index intersection depends on the specifics of your system. See [Index Intersection and Compound Indexes](https://docs.mongodb.com/manual/core/index-intersection/#index-intersection-compound-indexes) for more details.

从2.6版开始，MongoDB可以使用[索引交集](https://docs.mongodb.com/manual/core/index-intersection/)来完成查询。创建支持查询的复合索引还是依赖索引交集取决于系统的具体情况。更多详细信息，请参见 [索引交集和复合索引](https://docs.mongodb.com/manual/core/index-intersection/#index-intersection-compound-indexes)。



## Additional Considerations其他注意事项

Applications may encounter reduced performance during index builds, including limited read/write access to the collection. For more information on the index build process, see [Index Builds on Populated Collections](https://docs.mongodb.com/manual/core/index-creation/#index-operations), including the [Index Builds in Replicated Environments](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build) section.

应用程序在建立索引期间可能会遇到性能下降的情况，包括对集合的有限读/写访问权限。有关索引构建过程的更多信息，请参见 [“填充集合上](https://docs.mongodb.com/manual/core/index-creation/#index-operations)的[索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)，包括“ [复制环境中](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)的 [索引构建”](https://docs.mongodb.com/manual/core/index-creation/#index-operations-replicated-build)部分。

Some drivers may specify indexes, using `NumberLong(1)` rather than `1` as the specification. This does not have any affect on the resulting index.

一些驱动程序可能使用`NumberLong(1)`而不是 `1`将规范指定为索引。这对结果索引没有任何影响。



原文链接：https://docs.mongodb.com/manual/core/index-compound/

译者：莫薇

update：小芒果