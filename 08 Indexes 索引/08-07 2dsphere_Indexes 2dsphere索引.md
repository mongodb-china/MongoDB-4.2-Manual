-   [Indexes](https://docs.mongodb.com/manual/indexes/) >
-   `2dsphere`  Indexes

# `2dsphere`  Indexes[](https://docs.mongodb.com/manual/core/2dsphere/#dsphere-indexes "Permalink to this headline")
# `2dsphere`索引

On this page
本页包含

-   [Overview]概述(https://docs.mongodb.com/manual/core/2dsphere/#overview)
-   [Versions]版本变更(https://docs.mongodb.com/manual/core/2dsphere/#versions)
-   [Considerations]思考(https://docs.mongodb.com/manual/core/2dsphere/#considerations)
-   [Create a  `2dsphere`  Index]创建一个`2dsphere`索引(https://docs.mongodb.com/manual/core/2dsphere/#create-a-2dsphere-index)

## Overview[](https://docs.mongodb.com/manual/core/2dsphere/#overview "Permalink to this headline")
## 概述

A  `2dsphere`  index supports queries that calculate geometries on an earth-like sphere.  `2dsphere`  index supports all MongoDB geospatial queries: queries for inclusion, intersection and proximity. For more information on geospatial queries, see  [Geospatial Queries](https://docs.mongodb.com/manual/geospatial-queries/).
`2dsphere`索引支持计算类似地球的球体上的几何形状的查询。`2dsphere`索引支持所有MongoDB地理空间查询：包含、相交和邻近度查询。 有关地理空间查询的更多信息，请参见[地理空间查询](https://docs.mongodb.com/manual/geospatial-queries/)。

The  `2dsphere`  index supports data stored as  [GeoJSON objects](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)  _and_  [legacy coordinate pairs](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy)  (See also  [2dsphere Indexed Field Restrictions](https://docs.mongodb.com/manual/core/2dsphere/#dsphere-data-restrictions)). For legacy coordinate pairs, the index converts the data to GeoJSON  [Point](https://docs.mongodb.com/manual/reference/geojson/#geojson-point).
`2dsphere`索引支持以[GeoJSON对象](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)和[旧版坐标对](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy)（另请参见[2dsphere索引字段限制](https://docs.mongodb.com/manual/core/2dsphere/#dsphere-data-restrictions)）存储的数据。对于旧版坐标对，索引会将数据转换为GeoJSON [Point](https://docs.mongodb.com/manual/reference/geojson/#geojson-point)。

## Versions[](https://docs.mongodb.com/manual/core/2dsphere/#versions "Permalink to this headline")
## 版本变更

`2dsphere`  Index Version
`2dsphere`索引版本

Description
描述

Version 3
版本 3

MongoDB 3.2 introduces a version 3 of  `2dsphere`  indexes. Version 3 is the default version of  `2dsphere`  indexes created in MongoDB 3.2 and later.
MongoDB 3.2 引入了 `2dsphere`索引的版本3。版本3是在MongoDB 3.2及更高版本中创建`2dsphere`索引时的默认版本。

Version 2
版本 2

MongoDB 2.6 introduces a version 2 of  `2dsphere`  indexes. Version 2 is the default version of  `2dsphere`  indexes created in MongoDB 2.6 and 3.0 series.
MongoDB 2.6引入了`2dsphere`索引的版本2。 版本2是在MongoDB 2.6和3.0系列中创建`2dsphere`索引时的默认版本。

To override the default version and specify a different version, include the option  `{  "2dsphereIndexVersion":  <version>  }`  when creating the index.
要覆盖默认版本并指定其他版本，请在创建索引时包含选项`{“ 2dsphereIndexVersion”：<version>}`。

### `sparse`  Property[](https://docs.mongodb.com/manual/core/2dsphere/#sparse-property "Permalink to this headline")
### `sparse`属性

Version 2 and later  `2dsphere`  indexes are always  [sparse](https://docs.mongodb.com/manual/core/index-sparse/)  and ignore the  [sparse](https://docs.mongodb.com/manual/core/index-sparse/)  option. If a document lacks a  `2dsphere`  index field (or the field is  `null`  or an empty array), MongoDB does not add an entry for the document to the index. For inserts, MongoDB inserts the document but does not add to the  `2dsphere`  index.
版本2和更高版本的`2dsphere`索引始终为[sparse](https://docs.mongodb.com/manual/core/index-sparse/)且忽略[sparse](https://docs.mongodb.comhttps://docs.mongodb.com/manual/core/index-sparse/)选项。如果文档缺少`2dsphere`索引所在字段（或者该字段为null或空数组），则MongoDB不会将文档条目添加到索引中。对于插入，MongoDB会插入文档，但不添加到`2dsphere`索引。

For a compound index that includes a  `2dsphere`  index key along with keys of other types, only the  `2dsphere`  index field determines whether the index references a document.
对于包含`2dsphere`索引键以及其他类型键的复合索引，该索引是否引用文档只取决于`2dsphere`索引字段。

Earlier versions of MongoDB only support  `2dsphere  (Version  1)`  indexes.  `2dsphere  (Version  1)`  indexes are  _not_  sparse by default and will reject documents with  `null`  location fields.
MongoDB的早期版本仅支持`2dsphere (Version 1)`索引。 默认情况下，`2dsphere (Version 1)`索引不是稀疏索引，并且拒绝该字段为空的文档。

### Additional GeoJSON Objects[](https://docs.mongodb.com/manual/core/2dsphere/#additional-geojson-objects "Permalink to this headline")
### 其他GeoJSON对象

Version 2 and later  `2dsphere`  indexes includes support for additional GeoJSON object:  [MultiPoint](https://docs.mongodb.com/manual/reference/geojson/#geojson-multipoint),  [MultiLineString](https://docs.mongodb.com/manual/reference/geojson/#geojson-multilinestring),  [MultiPolygon](https://docs.mongodb.com/manual/reference/geojson/#geojson-multipolygon), and  [GeometryCollection](https://docs.mongodb.com/manual/reference/geojson/#geojson-geometrycollection). For details on all supported GeoJSON objects, see  [GeoJSON Objects](https://docs.mongodb.com/manual/reference/geojson/).
版本2和更高版本的`2dsphere`索引包含对其他GeoJSON对象的支持：[MultiPoint](https://docs.mongodb.com/manual/reference/geojson/#geojson-multipoint)，[MultiLineString](https://docs.mongodb.com/manual/reference/geojson/#geojson-multilinestring)，[MultiPolygon](https://docs.mongodb.com/manual/reference/geojson/#geojson-multipolygon)和[GeometryCollection](https://docs.mongodb.com/manual/reference/geojson/#geojson-geometrycollection)。有关所有受支持的GeoJSON对象的详细信息，请参见[GeoJSON对象](https://docs.mongodb.com/manual/reference/geojson/)。

## Considerations[](https://docs.mongodb.com/manual/core/2dsphere/#considerations "Permalink to this headline")
## 思考

### `geoNear`  and  `$geoNear`  Restrictions[](https://docs.mongodb.com/manual/core/2dsphere/#geonear-and-geonear-restrictions "Permalink to this headline")
### `geoNear`和`$geoNear`的限制

Starting in MongoDB 4.0, you can specify a  `key`  option to the  [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear "$geoNear")  pipeline stage to indicate the indexed field path to use. This allows the  [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear "$geoNear")  stage to be used on a collection that has multiple  `2dsphere`  index and/or multiple  [2d](https://docs.mongodb.com/manual/core/2d/)  index:
从MongoDB 4.0开始，您可以为[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear "$geoNear")管道指定一个`key`选项以明确指示要使用的索引字段路径。这使得[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear "$geoNear")在具有多个`2dsphere`索引或多个[2d](https://docs.mongodb.com/manual/core/2d/)索引的文档中也能被使用：

-   If your collection has multiple  `2dsphere`  index and/or multiple  [2d](https://docs.mongodb.com/manual/core/2d/)  index, you must use the  `key`  option to specify the indexed field path to use.
- 如果您的集合具有多个`2dsphere`索引或多个[2d](https://docs.mongodb.com/manual/core/2d/)索引，则必须使用`key`选项来指定使用哪个索引字段路径。
-   If you do not specify the  `key`, you cannot have multiple  `2dsphere`  index and/or multiple  [2d](https://docs.mongodb.com/manual/core/2d/)  index since without the  `key`, index selection among multiple  `2d`  indexes or  `2dsphere`  indexes is ambiguous.
- 如果未指定`key`，您将无法使用多个`2dsphere`索引或多个[2d](https://docs.mongodb.com/manual/core/2d/)索引。 因为没有指定`key`时，在多个`2d`索引或`2dsphere`索引中选择索引将变得无法明确。

NOTE
注意

If you do not specify the  `key`, and you have at most only one  `2dsphere`  index index and/or only one  `2dsphere`  index index, MongoDB looks first for a  `2d`  index to use. If a  `2d`  index does not exists, then MongoDB looks for a  `2dsphere`  index to use.
如果您不指定`key`，您将最多只能拥有一个`2dsphere`索引或一个`2dsphere`索引，MongoDB首先寻找`2d`索引。 如果不存在`2d`索引，则MongoDB会寻找`2dsphere`索引。

### Shard Key Restrictions[](https://docs.mongodb.com/manual/core/2dsphere/#shard-key-restrictions "Permalink to this headline")
### 分片键限制

You cannot use a  `2dsphere`  index as a  [shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)  when sharding a collection. However, you can create a geospatial index on a sharded collection by using a different field as the shard key.
对集合做分片时，不能将`2dsphere`索引用作[分片键](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)。 但是，您可以通过使用一个不同的字段作为分片键来在分片集合上创建地理空间索引。

### `2dsphere`  Indexed Field Restrictions[](https://docs.mongodb.com/manual/core/2dsphere/#dsphere-indexed-field-restrictions "Permalink to this headline")
### `2dsphere`索引字段限制

Fields with  [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/#)  indexes must hold geometry data in the form of  [coordinate pairs](https://docs.mongodb.com/manual/reference/glossary/#term-legacy-coordinate-pairs)  or  [GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson)  data. If you attempt to insert a document with non-geometry data in a  `2dsphere`  indexed field, or build a  `2dsphere`  index on a collection where the indexed field has non-geometry data, the operation will fail.
具有[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/#)索引的字段必须包含[坐标对](https://docs.mongodb.com/manual/reference/glossary/#term-legacy-coordinate-pairs)或[GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson)形式的数据。如果您尝试插入一个在`2dsphere`索引字段中包含非几何数据的文档，或者在一个索引字段中包含非几何数据的集合上构建`2dsphere`索引，该操作将失败。

## Create a  `2dsphere`  Index[](https://docs.mongodb.com/manual/core/2dsphere/#create-a-2dsphere-index "Permalink to this headline")
## 创建`2dsphere`索引

To create a  `2dsphere`  index, use the  [`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex "db.collection.createIndex()")  method and specify the string literal  `"2dsphere"`  as the index type:
要创建`2dsphere`索引，请使用[`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex "db.collection.createIndex()") 方法并指定字符串`"2dsphere"`作为索引类型：

```sql
db.collection.createIndex( { <location field> : "2dsphere" } )
```

where the  `<location  field>`  is a field whose value is either a  [GeoJSON object](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)  or a  [legacy coordinates pair](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy).
其中的`<location  field>`是其值为[GeoJSON对象](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)或[旧式坐标对](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy)的字段。

Unlike a compound  [2d](https://docs.mongodb.com/manual/core/2d/)  index which can reference one location field and one other field, a  [compound](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)  `2dsphere`  index can reference multiple location and non-location fields.
与只能引用一个位置字段和另一个字段的复合[2d](https://docs.mongodb.com/manual/core/2d/)索引不同的是，[复合](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)`2dsphere`索引可以引用多个位置字段及非位置字段。

For the following examples, consider a collection  `places`  with documents that store location data as  [GeoJSON Point](https://docs.mongodb.com/manual/reference/geojson/#geojson-point)  in a field named  `loc`:
以下示例，基于一个`places`集合，该集合的文档将位置数据以[GeoJSON Point](https://docs.mongodb.com/manual/reference/geojson/#geojson-point)形式存储在`loc`字段中：

```sql
db.places.insert(
   {
      loc : { type: "Point", coordinates: [ -73.97, 40.77 ] },
      name: "Central Park",
      category : "Parks"
   }
)

db.places.insert(
   {
      loc : { type: "Point", coordinates: [ -73.88, 40.78 ] },
      name: "La Guardia Airport",
      category : "Airport"
   }
)
```
### Create a  `2dsphere`  Index[](https://docs.mongodb.com/manual/core/2dsphere/#id1 "Permalink to this headline")
### 创建`2dsphere`索引

The following operation creates a  [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/#)  index on the location field  `loc`:
以下操作在位置字段`loc`上创建一个[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/#)索引：

``` sql
db.places.createIndex( { loc : "2dsphere" } )
```

### Create a Compound Index with  `2dsphere`  Index Key[](https://docs.mongodb.com/manual/core/2dsphere/#create-a-compound-index-with-2dsphere-index-key "Permalink to this headline")
### 使用`2dsphere`索引键创建复合索引

A  [compound index](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)  can include a  `2dsphere`  index key in combination with non-geospatial index keys. For example, the following operation creates a compound index where the first key  `loc`  is a  `2dsphere`  index key, and the remaining keys  `category`  and  `names`  are non-geospatial index keys, specifically descending (`-1`) and ascending (`1`) keys respectively.
[复合索引](https://docs.mongodb.com/manual/core/index-compound/#index-type-compound)可以包含`2dsphere`索引键和非地理空间索引键。例如，以下操作将创建一个复合索引，其中第一个键`loc`是`2dsphere`索引键，其余键`category`和`names`是非地理空间索引键，并分别指定降序（`-1`）和升序（`1`）。

```sql
db.places.createIndex( { loc : "2dsphere" , category : -1, name: 1 } )
```

Unlike the  [2d](https://docs.mongodb.com/manual/core/2d/)  index, a compound  `2dsphere`  index does not require the location field to be the first field indexed. For example:
与[2d](https://docs.mongodb.com/manual/core/2d/)索引不同，复合`2dsphere`索引不需要将位置字段作为第一个索引字段。 例如：

```sql
db.places.createIndex( { category : 1 , loc : "2dsphere" } )
```

原文链接：[https://docs.mongodb.com/manual/core/2dsphere/](https://docs.mongodb.com/manual/core/2dsphere/)

← [Wildcard Index Restrictions](https://docs.mongodb.com/manual/reference/index-wildcard-restrictions/ "Previous Section: Wildcard Index Restrictions")[Query a 2dsphere Index](https://docs.mongodb.com/manual/tutorial/query-a-2dsphere-index/ "Next Section: Query a 2dsphere Index") →
←[通配符索引限制](https://docs.mongodb.com/manual/reference/index-wildcard-restrictions/ "Previous Section: Wildcard Index Restrictions")[查询2dsphere索引](https://docs.mongodb.com/manual/tutorial/query-a-2dsphere-index/ "Next Section: Query a 2dsphere Index")→

原文链接：[https://docs.mongodb.com/manual/core/2dsphere/](https://docs.mongodb.com/manual/core/2dsphere/)

译者：周正
