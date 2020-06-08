
In addition to the numerous index types MongoDB supports, indexes can also have various properties. The following documents detail the index properties that you can select when building an index.

除了 MongoDB支持的众多索引类型外，索引还可以具有各种属性。以下文档详细介绍了在建立索引时可以选择的索引属性。


### [TTL Indexes TTL索引](https://docs.mongodb.com/manual/core/index-ttl/)

The TTL index is used for TTL collections, which expire data after a period of time.

TTL索引用于TTL收集，这些收集会在一段时间后使数据过期。

### [Unique Indexes 唯一索引](https://docs.mongodb.com/manual/core/index-unique/)

A unique index causes MongoDB to reject all documents that contain a duplicate value for the indexed field.

唯一索引会导致MongoDB拒绝所有包含索引字段重复值的文档。

### [Partial Indexes 部分索引](https://docs.mongodb.com/manual/core/index-partial/)

A partial index indexes only documents that meet specified filter criteria.

部分索引仅索引符合指定过滤条件的文档。

### [Case Insensitive Indexes 不区分大小写索引](https://docs.mongodb.com/manual/core/index-case-insensitive/)

A case insensitive index disregards the case of the index key values.

不区分大小写的索引会忽略索引键值的大小写。

### [Sparse Indexes 稀疏索引](https://docs.mongodb.com/manual/core/index-sparse/)

A sparse index does not index documents that do not have the indexed field.

稀疏索引不会索引没有索引字段的文档。


-----------------

## Index Properties[](#index-properties "Permalink to this headline")   索引属性

### Unique Indexes[](#unique-indexes "Permalink to this headline")    唯一索引

The  [unique](https://docs.mongodb.com/manual/core/index-unique.html)  property for an index causes MongoDB to reject duplicate values for the indexed field. Other than the unique constraint, unique indexes are functionally interchangeable with other MongoDB indexes.
开启[unique](https://docs.mongodb.com/manual/core/index-unique.html)选项，索引对应字段具有唯一性，对应字段拒绝重复值。除唯一性约束外，在功能上，索引的unique特性可与其他特性交替使用。

### Partial Indexes[](#partial-indexes "Permalink to this headline")   部分索引

New in version 3.2.
3.2版本新特性

[Partial indexes](https://docs.mongodb.com/manual/core/index-partial.html)  only index the documents in a collection that meet a specified filter expression. By indexing a subset of the documents in a collection, partial indexes have lower storage requirements and reduced performance costs for index creation and maintenance.
[Partial](https://docs.mongodb.com/manual/core/index-partial.html)特性相关选项设置后，将仅索引集合中满足指定筛选表达式的文档。对集合中的文档子集创建索引，设置了partial特性的索引将占用更低的存储，并降低mongodb创建索引和维护索引的性能开销。

Partial indexes offer a superset of the functionality of sparse indexes and should be preferred over sparse indexes.
在功能上，索引的Partial特性是sparse特性的超集，当一个索引同时拥有两种特性时，以Partial特性优先。

### Sparse Indexes[](#sparse-indexes "Permalink to this headline")  稀疏索引

The  [sparse](https://docs.mongodb.com/manual/core/index-sparse.html)  property of an index ensures that the index only contain entries for documents that have the indexed field. The index skips documents that  _do not_  have the indexed field.
索引的sparse特性确保只对存在索引字段的文档创建索引。创建索引时将会跳过那些没有对应字段值的文档。

You can combine the sparse index option with the unique index option to prevent inserting documents that have duplicate values for the indexed field(s) and skip indexing documents that lack the indexed field(s).
你或许可以将sparse选项和unique选项结合使用，以防止索引字段插入重复值，并对对应索引字段缺失的文档不创建索引，提升数据库效率。

### TTL Indexes[](#ttl-indexes "Permalink to this headline")  TTL索引

[TTL indexes](https://docs.mongodb.com/manual/core/index-ttl.html)  are special indexes that MongoDB can use to automatically remove documents from a collection after a certain amount of time. This is ideal for certain types of information like machine generated event data, logs, and session information that only need to persist in a database for a finite amount of time.
索引的[TTL](https://docs.mongodb.com/manual/core/index-ttl.html)特性，允许MongoDB在一定时间后自动从集合中移除文档。这非常适合某些类型的信息，例如：机器生成的事件数据、日志和会话信息，这些信息只需要在数据库中保留有限的时间。

See:  [Expire Data from Collections by Setting TTL](https://docs.mongodb.com/manual/tutorial/expire-data.html)  for implementation instructions.
有关实现说明，请参见：[Expire Data from Collections by Setting TTL](https://docs.mongodb.com/manual/tutorial/expire-data.html)



原文链接：https://docs.mongodb.com/manual/core/index-properties/

译者：程哲欣
