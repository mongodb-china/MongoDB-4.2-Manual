# FAQ: MongoDB Storage 常见问题解答:MongoDB 存储

>  On this page
>
> - [Storage Engine Fundamentals 存储引擎基础](https://docs.mongodb.com/v4.2/faq/storage/#storage-engine-fundamentals)
> - [Can you mix storage engines in a replica set? 是否支持在副本集中混用存储引擎？](https://docs.mongodb.com/v4.2/faq/storage/#can-you-mix-storage-engines-in-a-replica-set)
> - [WiredTiger Storage Engine WiredTiger存储引擎](https://docs.mongodb.com/v4.2/faq/storage/#wiredtiger-storage-engine)
> - [Data Storage Diagnostics 数据存储诊断](https://docs.mongodb.com/v4.2/faq/storage/#data-storage-diagnostics)

This document addresses common questions regarding MongoDB’s storage system.<br>本文档回答了有关MongoDB存储系统的常见问题。

## Storage Engine Fundamentals 存储引擎基础

### What is a storage engine? 什么是存储引擎？

A storage engine is the part of a database that is responsible for managing how data is stored, both in memory and on disk. Many databases support multiple storage engines, where different engines perform better for specific workloads. For example, one storage engine might offer better performance for read-heavy workloads, and another might support a higher throughput for write operations.<br>存储引擎是数据库的一部分，负责管理如何在内存和磁盘上存储数据。许多数据库支持多个存储引擎，其中不同的引擎对于特定的工作负载可能性能更好。例如，一个存储引擎可能为包含大量读取操作的工作负载提供更好的性能，而另一个引擎则可能为写入操作提供更高的吞吐量。

**SEE ALSO 另请参阅**

[Storage Engines 存储引擎](https://docs.mongodb.com/v4.2/core/storage-engines/)

## Can you mix storage engines in a replica set? 是否可以在副本集中混用不同的存储引擎？

Yes. You can have replica set members that use different storage engines (WiredTiger and in-memory)<br>可以。您可以让副本集成员使用不同的存储引擎（比如WiredTiger和内存引擎）

> **NOTE 注意：**
>
> Starting in version 4.2, MongoDB removes the deprecated MMAPv1 storage engine.<br>从4.2版本以后，MongoDB彻底移除了废弃的MMAPv1存储引擎。

## WiredTiger Storage Engine WiredTiger存储引擎

### Can I upgrade an existing deployment to WiredTiger? 是否可以将现有的部署升级到WiredTiger引擎?

Yes. See:<br>

可以，请参考：

- [Change Standalone to WiredTiger 将单机节点转为WiredTiger](https://docs.mongodb.com/v4.2/tutorial/change-standalone-wiredtiger/)
- [Change Replica Set to WiredTiger 将副本集转为WiredTiger](https://docs.mongodb.com/v4.2/tutorial/change-replica-set-wiredtiger/)
- [Change Sharded Cluster to WiredTiger 将分片集群转为WiredTiger](https://docs.mongodb.com/v4.2/tutorial/change-sharded-cluster-wiredtiger/)

### How much compression does WiredTiger provide? WiredTiger引擎能提供多少的压缩（比例）？

The ratio of compressed data to uncompressed data depends on your data and the compression library used. By default, collection data in WiredTiger use [Snappy block compression](https://docs.mongodb.com/v4.2/reference/glossary/#term-snappy); [zlib](https://docs.mongodb.com/v4.2/reference/glossary/#term-zlib) and [zstd](https://docs.mongodb.com/v4.2/reference/glossary/#term-zstd) compression is also available. Index data use [prefix compression](https://docs.mongodb.com/v4.2/reference/glossary/#term-prefix-compression) by default.<br>压缩数据与未压缩数据的比率取决于您的数据和所使用的压缩库。默认情况下，WiredTiger中的集合数据使用的是【Snappy块压缩】, 也可以使用【zlib】和【zstd】压缩。索引数据默认情况下使用【prefix压缩】。

### To what size should I set the WiredTiger internal cache? 我应该将WiredTiger内部缓存设置为多大？

With WiredTiger, MongoDB utilizes both the WiredTiger internal cache and the filesystem cache.<br>对于WiredTiger，MongoDB可以利用WiredTiger内部缓存和文件系统缓存。

Starting in MongoDB 3.4, the default WiredTiger internal cache size is the larger of either:<br>从MongoDB 3.4开始，默认的WiredTiger内部缓存大小是以下两者中的较大者：

- 50% of (RAM - 1 GB), or<br>（RAM - 1GB）/2
- 256 MB.<br>256MB

For example, on a system with a total of 4GB of RAM the WiredTiger cache will use 1.5GB of RAM (`0.5 * (4GB - 1 GB) = 1.5 GB`). Conversely, a system with a total of 1.25 GB of RAM will allocate 256 MB to the WiredTiger cache because that is more than half of the total RAM minus one gigabyte (`0.5 * (1.25 GB - 1GB) = 128 MB < 256 MB`).<br>例如，在总共有4GB RAM的系统上，WiredTiger缓存将使用1.5GB RAM**`（0.5 *（4 GB-1 GB）= 1.5 GB）`**。 相反，总内存为1.25GB的系统将为WiredTiger缓存分配256MB，因为256MB大于总RAM减去1GB的一半**`（0.5 *（1.25 GB-1 GB）= 128 MB <256 MB）`**。

> NOTE 注意：
>
> In some instances, such as when running in a container, the database can have memory constraints that are lower than the total system memory. In such instances, this memory limit, rather than the total system memory, is used as the maximum RAM available.<br>在某些情况下，例如在容器中运行时，数据库的内存限制可能低于系统总内存。在这种情况下，内存限制不再是系统总内存，而是最大可用RAM。
>
> To see the memory limit, see [`hostInfo.system.memLimitMB`](https://docs.mongodb.com/v4.2/reference/command/hostInfo/#hostInfo.system.memLimitMB).<br>关于内存限制，请参考【hostInfo.system.memLimitMB】。

By default, WiredTiger uses Snappy block compression for all collections and prefix compression for all indexes. Compression defaults are configurable at a global level and can also be set on a per-collection and per-index basis during collection and index creation.<br>默认情况下，WiredTiger对所有集合使用Snappy块压缩，对所有索引使用prefix压缩。压缩默认值是可以在全局级别配置的，也可以在集合和索引创建期间基于每个集合和每个索引进行设置。

Different representations are used for data in the WiredTiger internal cache versus the on-disk format:<br>WiredTiger内部缓存中的数据与磁盘格式使用不同的表示形式：

- Data in the filesystem cache is the same as the on-disk format, including benefits of any compression for data files. The filesystem cache is used by the operating system to reduce disk I/O.<br>文件系统缓存中的数据与磁盘上的格式相同，包括所有对数据文件进行压缩的收益。操作系统使用文件系统缓存来减少磁盘I/O。
- Indexes loaded in the WiredTiger internal cache have a different data representation to the on-disk format, but can still take advantage of index prefix compression to reduce RAM usage. Index prefix compression deduplicates common prefixes from indexed fields.<br>加载到WiredTiger内部缓存中的索引具有与磁盘格式不同的数据表示形式，但仍可以利用索引prefix压缩来减少RAM使用量。索引prefix压缩从索引字段中删除通用前缀。
- Collection data in the WiredTiger internal cache is uncompressed and uses a different representation from the on-disk format. Block compression can provide significant on-disk storage savings, but data must be uncompressed to be manipulated by the server.<br>WiredTiger内部缓存中的集合数据未经压缩，并使用与磁盘格式不同的表示形式。块压缩可以节省大量的磁盘存储空间，但是必须对数据进行解压缩才能由服务器端进行处理。

Via the filesystem cache, MongoDB automatically uses all free memory that is not used by the WiredTiger cache or by other processes.<br>通过文件系统缓存，MongoDB自动使用WiredTiger缓存或其他进程未使用的所有可用内存。

To adjust the size of the WiredTiger internal cache, see[`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/v4.2/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB) and [`--wiredTigerCacheSizeGB`](https://docs.mongodb.com/v4.2/reference/program/mongod/#cmdoption-mongod-wiredtigercachesizegb). Avoid increasing the WiredTiger internal cache size above its default value.<br>要调整WiredTiger引擎的内部缓存大小，请参考`storage.wiredTiger.engineConfig.cacheSizeGB`以及`--wiredTigerCacheSizeGB`参数。应避免将WiredTiger内部缓存的大小增加到其默认值以上。

> **NOTE 注意：**
>
> The [`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/v4.2/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB) limits the size of the WiredTiger internal cache. The operating system will use the available free memory for filesystem cache, which allows the compressed MongoDB data files to stay in memory. In addition, the operating system will use any free RAM to buffer file system blocks and file system cache.<br>`storage.wiredTiger.engineConfig.cacheSizeGB`限制WiredTiger内部缓存的大小。操作系统将使用可用的空闲内存进行文件系统缓存，从而允许压缩的MongoDB数据文件保留在内存中。此外，操作系统将使用任何可用的RAM来缓冲文件系统块和文件系统缓存。
>
> To accommodate the additional consumers of RAM, you may have to decrease WiredTiger internal cache size.<br>为了容纳更多的RAM使用者，您可能需要降低WiredTiger内部缓存的大小。

The default WiredTiger internal cache size value assumes that there is a single [`mongod`](https://docs.mongodb.com/v4.2/reference/program/mongod/#bin.mongod) instance per machine. If a single machine contains multiple MongoDB instances, then you should decrease the setting to accommodate the other [`mongod`](https://docs.mongodb.com/v4.2/reference/program/mongod/#bin.mongod) instances.<br>默认的WiredTiger内部缓存大小值假定每台计算机有且仅有一个mongod实例。如果一台机器包含多个MongoDB实例，则应减小设置以容纳其他mongod实例。

If you run [`mongod`](https://docs.mongodb.com/v4.2/reference/program/mongod/#bin.mongod) in a container (e.g. `lxc`, `cgroups`, Docker, etc.) that does *not* have access to all of the RAM available in a system, you must set [`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/v4.2/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB) to a value less than the amount of RAM available in the container. The exact amount depends on the other processes running in the container. See [`memLimitMB`](https://docs.mongodb.com/v4.2/reference/command/hostInfo/#hostInfo.system.memLimitMB).<br>如果您在**无法**访问系统中所有可用RAM的容器（例如`lxc`，`cgroups`，`Docker`等）中运行mongod，则必须将`storage.wiredTiger.engineConfig.cacheSizeGB`设置为小于该容器中可用的RAM数量。确切的数量取决于容器中运行的其他进程。请参考【`memLimitMB`】。

To view statistics on the cache and eviction rate, see the [`wiredTiger.cache`](https://docs.mongodb.com/v4.2/reference/command/serverStatus/#serverstatus.wiredTiger.cache) field returned from the [`serverStatus`](https://docs.mongodb.com/v4.2/reference/command/serverStatus/#dbcmd.serverStatus) command.<br>要查看有关缓存和淘汰率的统计信息，请参阅`serverStatus`命令返回的`wiredTiger.cache`字段的相关内容。

### How frequently does WiredTiger write to disk? WiredTiger引擎多久写一次磁盘？

- **Checkpoints 检查点** 

  Starting in version 3.6, MongoDB configures WiredTiger to create checkpoints (i.e. write the snapshot data to disk) at intervals of 60 seconds. In earlier versions, MongoDB sets checkpoints to occur in WiredTiger on user data at an interval of 60 seconds or when 2 GB of journal data has been written, whichever occurs first.<br>从3.6版开始，MongoDB将WiredTiger配置为以60秒的间隔创建检查点（即将快照数据写入磁盘）。在早期的版本中，MongoDB将在WiredTiger中以60秒的间隔或在写满2GB日志数据时对用户数据进行创建检查点操作，两个条件中任意一个满足即可。

- **Journal Data 日志数据**

  WiredTiger syncs the buffered journal records to disk upon any of the following conditions:<br>WiredTiger在以下任一情况满足时会将缓存的日记记录同步到磁盘：
  
  - For replica set members (primary and secondary members),<br>对于副本集成员（主或者从节点成员），
  
    - If there are operations waiting for oplog entries. Operations that can wait for oplog entries include:<br>是否有等待oplog条目的操作。需要等待oplog条目的操作包括：
      - forward scanning queries against the oplog <br>针对oplog转发扫描查询
      - read operations performed as part of [causally consistent sessions](https://docs.mongodb.com/v4.2/core/read-isolation-consistency-recency/#causal-consistency)<br>读取操作作为因果一致性会话的一部分
    - Additionally for secondary members, after every batch application of the oplog entries.<br>另外，对于从节点成员，在每批oplog条目被应用之后。
  
  - If a write operation includes or implies a write concern of [`j: true`](https://docs.mongodb.com/v4.2/reference/write-concern/#writeconcern.j).<br>如果写操作包括或隐含了`j：true`的写关注。
  
    >  NOTE 注意：
    >
    > Write concern [`"majority"`](https://docs.mongodb.com/v4.2/reference/write-concern/#writeconcern."majority") implies `j: true` if the [`writeConcernMajorityJournalDefault`](https://docs.mongodb.com/v4.2/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) is true.<br>当`writeConcernMajorityJournalDefault`为`true`时，设置为`"majority"`的写关注隐含了`j:true`。
  
  - At every 100 milliseconds (See [`storage.journal.commitIntervalMs`](https://docs.mongodb.com/v4.2/reference/configuration-options/#storage.journal.commitIntervalMs)).<br>每隔100ms（参考`storage.journal.commitIntervalMs`）
  
  - When WiredTiger creates a new journal file. Because MongoDB uses a journal file size limit of 100 MB, WiredTiger creates a new journal file approximately every 100 MB of data.<br>WiredTiger创建新的日志文件时。由于MongoDB使用的日志文件大小限制为100MB，因此WiredTiger大约每100MB数据创建一个新的日志文件。

### How do I reclaim disk space in WiredTiger? 如何在WiredTiger引擎中回收磁盘空间？

The WiredTiger storage engine maintains lists of empty records in data files as it deletes documents. This space can be reused by WiredTiger, but will not be returned to the operating system unless under very specific circumstances.<br>WiredTiger存储引擎在删除文档时会维护数据文件中的空记录列表。WiredTiger可以重用此空间，但是并不会将其返回给操作系统，除非是在非常特殊的情况下。

The amount of empty space available for reuse by WiredTiger is reflected in the output of [`db.collection.stats()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.stats/#db.collection.stats) under the heading `wiredTiger.block-manager.file bytes availablefor reuse`.<br>WiredTiger引擎的可用于重用的空闲空间反映在`db.collection.stats()`的结果的`wiredTiger.block-manager.file bytes availablefor reuse`字段中。

To allow the WiredTiger storage engine to release this empty space to the operating system, you can de-fragment your data file. This can be achieved using the [`compact`](https://docs.mongodb.com/v4.2/reference/command/compact/#dbcmd.compact) command. For more information on its behavior and other considerations, see [`compact`](https://docs.mongodb.com/v4.2/reference/command/compact/#dbcmd.compact).<br>为了使WiredTiger存储引擎可以将这些空闲空间归还给操作系统，可以对数据文件进行碎片整理。这可以通过`compact`命令实现。更多相关信息及其他考虑，请参考【`compact`】。

## Data Storage Diagnostics 数据存储诊断

### How can I check the size of a collection? 如何确认一个集合的大小？

To view the statistics for a collection, including the data size, use the [`db.collection.stats()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.stats/#db.collection.stats) method from the [`mongo`](https://docs.mongodb.com/v4.2/reference/program/mongo/#bin.mongo) shell. The following example issues [`db.collection.stats()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.stats/#db.collection.stats) for the `orders` collection:<br>要查看集合的统计信息，包括数据大小，请使用mongo shell中的`db.collection.stats()`方法。以下示例为在`order`集合上执行`db.collection.stats()`：

```
db.orders.stats();
```

MongoDB also provides the following methods to return specific sizes for the collection:<br>MongoDB同样提供了以下方法来返回集合的具体大小：

- [`db.collection.dataSize()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.dataSize/#db.collection.dataSize) to return the uncompressed data size in bytes for the collection.<br>`db.collection.dataSize()`会返回集合中未压缩的数据大小，以字节为单位。
- [`db.collection.storageSize()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.storageSize/#db.collection.storageSize) to return the size in bytes of the collection on disk storage. If collection data is compressed (which is the [`default for WiredTiger`](https://docs.mongodb.com/v4.2/reference/program/mongod/#cmdoption-mongod-wiredtigercollectionblockcompressor)), the storage size reflects the compressed size and may be smaller than the value returned by [`db.collection.dataSize()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.dataSize/#db.collection.dataSize).<br>`db.collection.storageSize()`会返回集合在磁盘上占用的大小，以字节为单位。如果集合的数据是压缩过的（WiredTiger引擎默认带数据压缩），则存储大小反映的是压缩后的大小，可能会比`db.collection.dataSize()`返回的结果小一些。
- [`db.collection.totalIndexSize()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.totalIndexSize/#db.collection.totalIndexSize) to return the index sizes in bytes for the collection. If an index uses prefix compression (which is the [`default for WiredTiger`](https://docs.mongodb.com/v4.2/reference/program/mongod/#cmdoption-mongod-wiredtigerindexprefixcompression)), the returned size reflects the compressed size.<br>`db.collection.totalIndexSize`会返回集合中所有索引的大小，以字节为单位。如果一个索引使用了前缀压缩（WiredTiger引擎默认索引使用前缀压缩），则返回的大小反映的是压缩后的大小。

The following script prints the statistics for each database:<br>以下的脚本会输出每个数据库的统计信息：

```
db.adminCommand("listDatabases").databases.forEach(function (d) {
   mdb = db.getSiblingDB(d.name);
   printjson(mdb.stats());
})
```

The following script prints the statistics for each collection in each database:<br>以下的脚本会输出每个数据库中每个集合的统计信息：

```
db.adminCommand("listDatabases").databases.forEach(function (d) {
   mdb = db.getSiblingDB(d.name);
   mdb.getCollectionNames().forEach(function(c) {
      s = mdb[c].stats();
      printjson(s);
   })
})
```

### How can I check the size of the individual indexes for a collection? 如何确认集合中每个索引的大小？

To view the size of the data allocated for each index, use the [`db.collection.stats()`](https://docs.mongodb.com/v4.2/reference/method/db.collection.stats/#db.collection.stats) method and check the[`indexSizes`](https://docs.mongodb.com/v4.2/reference/command/collStats/#collStats.indexSizes) field in the returned document.<br>要查看为每个索引分配的数据大小，使用`db.collection.stats()`方法，然后查看返回结果文档中的`indexSizes`字段。

If an index uses prefix compression (which is the [`default for WiredTiger`](https://docs.mongodb.com/v4.2/reference/program/mongod/#cmdoption-mongod-wiredtigerindexprefixcompression)), the returned size for that index reflects the compressed size.<br>如果索引使用前缀压缩（这也是WiredTiger引擎的默认设置），返回的大小代表了压缩后的大小。



### How can I get information on the storage use of a database? 如何获得有关数据库存储使用的相关信息？

The [`db.stats()`](https://docs.mongodb.com/v4.2/reference/method/db.stats/#db.stats) method in the [`mongo`](https://docs.mongodb.com/v4.2/reference/program/mongo/#bin.mongo) shell returns the current state of the “active” database. For the description of the returned fields, see [dbStats Output](https://docs.mongodb.com/v4.2/reference/command/dbStats/#dbstats-output).<br>mongo shell中的`db.stats()`方法返回“激活”数据库的当前状态。有关返回字段的描述，请参考【dbStats输出】。



原文链接：https://docs.mongodb.com/v4.2/faq/storage/

译者：刘翔
