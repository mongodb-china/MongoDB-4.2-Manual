
# Index Builds on Populated Collections[](#index-builds-on-populated-collections "Permalink to this headline")   在集合上创建索引

On this page

-   [Behavior](#behavior)     行为
-   [Index Build Impact on Database Performance](#index-build-impact-on-database-performance)  索引构建对数据库性能的影响
-   [Index Builds in Replicated Environments](#index-builds-in-replicated-environments)  复制集环境的索引生成
-   [Build Failure and Recovery](#build-failure-and-recovery)  构建失败和恢复
-   [Monitor In Progress Index Builds](#monitor-in-progress-index-builds)   监视正在进行的索引生成
-   [Terminate In Progress Index Builds](#terminate-in-progress-index-builds)  终止正在进行的索引生成
-   [Index Build Process](#index-build-process)  索引生成过程

Changed in version MongoDB: 4.2
MongoDB 4.2版本新变化

MongoDB index builds against a populated collection require an exclusive read-write lock against the collection. Operations that require a read or write lock on the collection must wait until the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases the lock. MongoDB 4.2 uses an optimized build process that only holds the exclusive lock at the beginning and end of the index build. The rest of the build process yields to interleaving read and write operations.
对指定集合构建索引时会独占该集合的读写锁（注：构建索引时会锁表）。对集合构建索引时该集合读和写操作会加锁，直到[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程完成索引构建，该集合的读写操作锁才会释放。  MongoDB 4.2对该过程进行了优化，仅在索引生成的开始和结束保留独占锁，构建索引的其余过程可对该集合进行交错读写操作。（注：MongoDB 4.2缩短了索引创建时的锁表时间）

The build process is summarized as follows:
索引构建过程汇总如下：

1.  **Initialization  初始化**
    
    The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  takes an exclusive lock against the collection being indexed. This blocks all read and write operations to the collection until the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases the lock. Applications cannot access the collection during this time.
    [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 进程对正在编制索引的集合使用独占锁。所有对该集合的读写操作将阻塞直到[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 进程释放锁。在此期间，应用程序无法访问集合。

2.  **Data Ingestion and Processing  数据提取和加工**
    
    The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases all locks taken by the index build process before taking a series of intent locks against the collection being indexed. Applications can issue read and write operations against the collection during this time.
    [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程释放上一过程中获取的所有锁，然后针对被索引的集合获取一系列意向锁。在此期间，应用程序可以对集合发出读写操作。
    
3.  **Cleanup  清理**
    
    The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases all locks taken by the index build process before taking an exclusive lock against the the collection being indexed. This blocks all read and write operations to the collection until the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases the lock. Applications cannot access the collection during this time.
    [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程释放上一过程中获取的所有锁，然后针对被索引集合获取独占锁。这时将阻塞对该集合所有读写操作直到[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程释放锁。应用程序此时无法访问该集合。
    
4.  **Completion  完成**
    
    The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  marks the index as ready to use and releases all locks taken by the index build process.
    [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程标记索引状态为已可用，然后释放索引构建过程中的所有锁。

For a detailed description of index build locking behavior, see  [Index Build Process](#index-build-process). For more information on MongoDB locking behavior, see  [FAQ: Concurrency](../faq/concurrency.html).
索引构建过程中的加锁描述细节参见[Index Build Process](#index-build-process)章节。更深入了解MongoDB的加锁行为参见[FAQ: Concurrency](../faq/concurrency.html)。

## Behavior[](#behavior "Permalink to this headline")  行为

MongoDB 4.2 index builds fully replace the index build processes supported in previous MongoDB versions. MongoDB ignores the  `background`  index build option if specified to  [`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  or its shell helpers  [`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()")  and  [`createIndexes()`](../reference/method/db.collection.createIndexes.html#db.collection.createIndexes "db.collection.createIndexes()").
MongoDB 4.2索引构建过程完全替换了之前版本的索引构建过程。MongoDB忽视了[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  命令或其shell中[`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()") 和 [`createIndexes()`](../reference/method/db.collection.createIndexes.html#db.collection.createIndexes "db.collection.createIndexes()")函数的`background`选项。（注：MongoDB 4.2中无需在db.collectionName.createIndex()中设置{'background':true}）。


REQUIRES FEATURECOMPATIBILITYVERSION 4.2
需特性兼容版本设置为4.2版本

For MongoDB clusters upgraded from 4.0 to 4.2, you must set the  [feature compatibility version (fcv)](../reference/command/setFeatureCompatibilityVersion.html#view-fcv)  to  `4.2`  to enable the optimized build process. For more information on setting the fCV, see  [`setFeatureCompatibilityVersion`](../reference/command/setFeatureCompatibilityVersion.html#dbcmd.setFeatureCompatibilityVersion "setFeatureCompatibilityVersion").
当MongoDB集群从4.0版本升级到4.2版本时，需设置特性兼容版本选项到4.2版本，以启用该优化过程，参见[`setFeatureCompatibilityVersion`](../reference/command/setFeatureCompatibilityVersion.html#dbcmd.setFeatureCompatibilityVersion "setFeatureCompatibilityVersion")。

MongoDB 4.2 clusters running with fCV  `4.0`  only support 4.0 index builds.
MongoDB 4.2版本在特性兼容版本为4.0时，仅支持4.0的索引构建方式。

### Comparison to Foreground and Background Builds[](#comparison-to-foreground-and-background-builds "Permalink to this headline")  索引前后台创建的比较

Previous versions of MongoDB supported building indexes either in the foreground or background. Foreground index builds were fast and produced more efficient index data structures, but required blocking all read-write access to the parent database of the collection being indexed for the duration of the build. Background index builds were slower and had less efficient results, but allowed read-write access to the database and its collections during the build process.
先前版本的MongoDB支持前后台两种方式创建索引。前台构建索引方式速度更快且可生成更有效的数据结构，但在生成期间会阻塞该集合父数据库的所有读写操作。后台构建索引方式相比于前者速度更慢，且数据结构检索效率较低，但在索引构建过程中允许对数据库和正在构建索引集合的的读写访问。

Changed in version MongoDB: 4.2
MongoDB 4.2版本变化

MongoDB 4.2 index builds obtain an exclusive lock on only the collection being indexed during the start and end of the build process to protect metadata changes. The rest of the build process uses the yielding behavior of background index builds to maximize read-write access to the collection during the build. 4.2 index builds still produce efficient index data structures despite the more permissive locking behavior.
MongoDB 4.2版本仅在索引构建过程的开始和结束阶段，针对构建索引的集合获得独占锁（注：进行锁表），避免元数据变更。索引构建过程的其余阶段，使用后台构建索引方式，以保证索引生成期间最大化集合读写访问。

MongoDB 4.2 index build performance is at least on par with background index builds. For workloads with few or no updates received during the build process, 4.2 index builds builds can be as fast as a foreground index build on that same data.
MongoDB 4.2版本构建索引时的性能表现与基于后台的索引构建方式相当。索引构建期间，如果没有或者较低的更新操作负载下，4.2版本的索引构建速度可以与基于前台的索引构建方式一样快。

Use  [`db.currentOp()`](../reference/method/db.currentOp.html#db.currentOp "db.currentOp()")  to monitor the progress of ongoing index builds.
使用[`db.currentOp()`](../reference/method/db.currentOp.html#db.currentOp "db.currentOp()") 命令监测索引生成进度。

### Constraint Violations During Index Build[](#constraint-violations-during-index-build "Permalink to this headline")    索引构建期间的约束冲突

For indexes that enforce constraints on the collection, such as  [unique](index-unique.html)  indexes, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  checks all pre-existing and concurrently-written documents for violations of those constraints  _after_  the index build completes. Documents that violate the index constraints can exist during the index build. If any documents violate the index constraints at the end of the build, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  terminates the build and throws an error.
对集合具有强制约束作用的索引，例如：[unique](index-unique.html) 特性索引，  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程会在索引构建完成后对所有预先存在的和并发写入的文档进行约束性检查。如果任何文档违反了索引约束条件，[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程将终止构建并抛出错误。

For example, consider a populated collection  `inventory`. An administrator wants to create a unique index on the  `product_sku`  field. If any documents in the collection have duplicate values for  `product_sku`, the index build can still start successfully. If any violations still exist at the end of the build, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  terminates the build and throws an error.
举例：对`inventory`集合的`product_sku`属性构建unique特性索引。如果任意文档的`product_sku`属性有重复值，索引构建过程仍可成功开始，如果在构建结束时仍然存在任何冲突，[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程会终止构建并抛出错误。

Similarly, an application can successfully write documents to the  `inventory`  collection with duplicate values of  `product_sku`  while the index build is in progress. If any violations still exist at the end of the build, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  terminates the build and throws an error.
类似的，在索引构建的过程中，应用程序可成功对`inventory`集合写入`product_sku`属性值重复的文档。如果在构建结束时存在任何索引约束冲突，[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程会终止构建并抛出错误。

To mitigate the risk of index build failure due to constraint violations:
降低因违反约束而导致索引生成失败的风险：

-   Validate that no documents in the collection violate the index constraints.
-   校验集合中没有违反索引约束的文档。
-   Stop all writes to the collection from applications that cannot guarantee violation-free write operations.
-  停止所有可能违反该集合索引约束条件的应用程序写入操作。

## Index Build Impact on Database Performance[](#index-build-impact-on-database-performance "Permalink to this headline")  索引构建对数据库性能的影响

Index Builds During Write-Heavy Workloads
高负载写入时的索引构建

Building indexes during time periods where the target collection is under heavy write load can result in reduced write performance and longer index builds.
在目标集合处于高负载写入状态时执行索引构建操作，会造成写入性能下降和更长的索引构建时间。

Consider designating a maintenance window during which applications stop or reduce write operations against the collection. Start the index build during this maintenance window to mitigate the potential negative impact of the build process.
考虑指定一个维护窗口用于构建索引，在此期间停止或减少对目标集合的写入操作。以减少索引生成过程对性能的潜在负面影响。

Insufficient Available System Memory (RAM)
可用系统内存不足时的索引构建

[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  supports building one or more indexes on a collection.  [`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  uses a combination of memory and temporary files on disk to complete index builds. The default limit on memory usage for  [`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  is 500 megabytes, shared between all indexes built using a single  [`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  command. Once the memory limit is reached,  [`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")  uses temporary disk files in a subdirectory named  `_tmp`  within the  [`--dbpath`](../reference/program/mongod.html#cmdoption-mongod-dbpath)  directory to complete the build.
[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")命令支持在集合上构建多个索引。[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")命令同时使用内存和磁盘上的临时文件空间完成索引构建。[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")命令默认的内存使用限制是500MB，该空间在所有使用[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")命令生成的索引之间共享。一旦在构建索引时到达该空间限制，[`createIndexes`](../reference/command/createIndexes.html#dbcmd.createIndexes "createIndexes")命令将使用[`--dbpath`](../reference/program/mongod.html#cmdoption-mongod-dbpath)目录下`_tmp`文件夹下的临时磁盘文件空间，用于完成索引构建。

You can override the memory limit by setting the  [`maxIndexBuildMemoryUsageMegabytes`](../reference/parameters.html#param.maxIndexBuildMemoryUsageMegabytes "maxIndexBuildMemoryUsageMegabytes")  server parameter. Setting a higher memory limit may result in faster completion of index builds larger than 500 megabytes. However, setting this limit too high relative to the unused RAM on your system can result in memory errors.
你可以自定义[`maxIndexBuildMemoryUsageMegabytes`](../reference/parameters.html#param.maxIndexBuildMemoryUsageMegabytes "maxIndexBuildMemoryUsageMegabytes")参数，更改该空间大小限制。设置更高的内存空间可以在索引大小超500MB时更快完成索引构建。当然，该参数设置过高也会导致占用太多无用内存造成系统内存错误。

If the host machine has limited available free RAM, you may need to schedule a maintenance period to increase the total system RAM before you can modify the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  RAM usage.
如果主机内存有限，你可能需要安排一个维护期，增加整个系统内存，再更改[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程中的内存参数设置。

## Index Builds in Replicated Environments[](#index-builds-in-replicated-environments "Permalink to this headline")  复制集中的索引构建

To minimize the impact of building an index on:
为了尽量降低构建索引时对如下方面的影响：

-   Replica Sets, use a rolling index build procedure as described in  [Build Indexes on Replica Sets](../tutorial/build-indexes-on-replica-sets.html).
- 复制集中，使用滚动索引生成策略，如[Build Indexes on Replica Sets](../tutorial/build-indexes-on-replica-sets.html)章节所述。
-   Sharded Clusters with Shard Replica Sets, use a rolling index build procedure as described in  [Build Indexes on Sharded Clusters](../tutorial/build-indexes-on-sharded-clusters.html).
- 拥有分片复制集的分片集群中，使用滚动索引生成策略，如[Build Indexes on Sharded Clusters](../tutorial/build-indexes-on-sharded-clusters.html)章节所述。

You can alternatively start the index build on the  [primary](../reference/glossary.html#term-primary). Once the index build completes, the secondaries replicate and start the index build. Consider the following risks  _before_  starting a replicated index build:
你可以在主节点执行索引构建。索引生成完成后，从节点进行复制并开始索引构建。复制集中在开始构建索引之前请注意如下风险：

Secondaries May Fall Out of Sync
从节点可能不进行同步。

Secondary index builds block the application of replicated transactions on a sharded cluster  _if_  that transaction includes writes to the collection being indexed. Similarly, replicated metadata operations against the collection being indexed also stall behind the index build. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  cannot apply any further oplog entries until the index build completes.
从节点的索引构建将阻塞应用正在执行的对构建索引集合的事务操作。 [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在构建索引完成前将无法使用任何oplog。

Replicated write operation to the collection being indexed can also stall behind the index build  _if_  the index build is holding an exclusive lock at the time of the operation or command. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  cannot apply any further oplog entries until the index build releases the exclusive lock. If replication stalls for longer than the  [oplog window](replica-set-oplog.html#replica-set-oplog-sizing)  on that secondary, the secondary falls out of sync and requires  [resynchronization](replica-set-sync.html#replica-set-initial-sync)  to recover.
如果索引生成在执行操作或命令时持有独占锁，则对被索引集合的复制写操作也可能被延迟到索引构建完成之后。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 进程无法使用任何oplog直到锁释放。如果复制延迟时间超过从节点的[oplog window](replica-set-oplog.html#replica-set-oplog-sizing)，从节点将不进行同步，需要通过重新同步来恢复。

Use  [`rs.printReplicationInfo()`](../reference/method/rs.printReplicationInfo.html#rs.printReplicationInfo "rs.printReplicationInfo()")  on each replica set member to validate the time covered by the oplog size configured for that member  _prior_  to starting the index build. You can  [increase the oplog size](../tutorial/change-oplog-size.html)  to mitigate the likelihood of a secondary falling out of sync. For example, setting an oplog window size that can cover 72 hours of operations ensures that secondaries can tolerate at least that much replication lag.
在构建索引之前，使用[`rs.printReplicationInfo()`](../reference/method/rs.printReplicationInfo.html#rs.printReplicationInfo "rs.printReplicationInfo()")命令鉴别每个副本集成员配置的时间区间中可处理的oplog大小。你可以增大oplog大小[increase the oplog size](../tutorial/change-oplog-size.html)，降低从节点不同步的概率。例如，设置oplog的窗口可以覆盖72个小时的操作记录，只要确保从节点可以容忍这么久的复制延迟。

Alternatively, build indexes during a maintenance window in which applications cease issuing distributed transactions, write operations, or metadata commands that affect the collection being indexed.
或者在维护时间窗口中执行索引构建，应用程序停止对该索引集合的所有事物操作、写操作和元数据操作。

Secondary Index Builds May Stall Read and Write Operations
从节点的索引构建可能造成读写操作延迟。

MongoDB 4.2 index builds obtain an exclusive lock on the collection being indexed at the start and end of the build process. While a secondary index build holds the exclusive lock, any read or write operations that depends on the secondary stall until the build releases that lock.
MongoDB 4.2索引生成过程的开始和结束时获取正在索引的集合的独占锁。当从节点索引生成持有独占锁时，该从节点将暂停任何读写操作，直到索引生成释放该锁为止。

Secondaries Process Index Drops After Index Build Completes
索引生成完成后，辅助进程索引将删除。

Dropping the index on the primary before secondaries complete the replicated index build does not kill the secondary index builds. When the secondary replicates the index drop, it must wait until  _after_  the index build completes to apply the drop. Furthermore, since index drops are a metadata operation on the collection, the index drop stalls replication on that secondary.
在从节点完成索引构建的复制之前，在主节点删除索引，将不会中断从节点的构建。当从节点执行索引删除的复制操作时，其须等待之前的索引生成操作执行复制完毕。此外，由于索引删除是集合上的元数据操作，因此索引删除会暂停该从节点上的复制。

## Build Failure and Recovery[](#build-failure-and-recovery "Permalink to this headline")  构建失败和恢复

### Interrupted Index Builds on Standalone  `mongod`[](#interrupted-index-builds-on-standalone-mongod "Permalink to this headline")   单个`mongod`[](#interrupted-index-builds-on-standalone-mongod "Permalink to this headline")进程的索引构建中断

If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  shuts down during the index build, the index build job and all progress is lost. Restarting the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  does not restart the index build. You must re-issue the  [`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()")  operation to restart the index build.
如果[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在索引构建时终止了，索引构建任务和所有进程将丢失。重启[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程不会重新执行索引构建，你必须重新运行[`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()") 操作来重启索引构建。

### Interrupted Index Builds on a Primary  `mongod`[](#interrupted-index-builds-on-a-primary-mongod "Permalink to this headline")  主节点`mongod`[](#interrupted-index-builds-on-a-primary-mongod "Permalink to this headline")进程的索引构建中断

If the primary shuts down or steps down during the index build, the index build job and all progress is lost. Restarting the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  does not restart the index build. You must re-issue the  [`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()")  operation to restart the index build.
如果从节点在索引构建时停止了，索引构建任务和所有进程将丢失。你必须重新运行[`createIndex()`](../reference/method/db.collection.createIndex.html#db.collection.createIndex "db.collection.createIndex()") 操作来重启索引构建。

### Interrupted Index Builds on a Secondary  `mongod`[](#interrupted-index-builds-on-a-secondary-mongod "Permalink to this headline") 从节点`mongod`[](#interrupted-index-builds-on-a-secondary-mongod "Permalink to this headline")进程的索引构建中断

If a secondary shuts down during the index build, the index build job is persisted. Restarting the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  recovers the index build and restarts it  **from scratch**.
如果从节点在索引构建时停止了，索引构建任务将保留。重启 [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程将恢复索引构建并从头重新开始。

The startup process stalls behind any recovered index builds. All other operations, including replication, wait until the index builds complete. If the secondary’s oplog does not cover the time required to complete the index build, the secondary may fall out of sync with the rest of the replica set and require  [resynchronization](replica-set-sync.html#replica-set-initial-sync).
启动进程会在任意已恢复的索引生成之后暂停。所有操作，包括复制将进入等待直到索引构建完成。如果从节点的oplog未在时间窗口区间完成缩影索引构建的复制，从节点不再进行这部分oplog的复制集的同步，需要重新同步恢复。

If you restart the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  as a standalone (i.e. removing or commenting out  [`replication.replSetName`](../reference/configuration-options.html#replication.replSetName "replication.replSetName")  or omitting  [`--replSetName`](../reference/program/mongod.html#cmdoption-mongod-replset)), the  `mongod`  still recovers the index build  **from scratch**. You can use the  [`storage.indexBuildRetry`](../reference/configuration-options.html#storage.indexBuildRetry "storage.indexBuildRetry")  configuration file setting or  [`--noIndexBuildRetry`](../reference/program/mongod.html#cmdoption-mongod-noindexbuildretry)  command line option to skip the index build on start up.
如果你重启了[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程作为独立的复制集节点实例或删除[`--replSetName`](../reference/program/mongod.html#cmdoption-mongod-replset))。`mongod`进程仍将从头恢复索引构建。你可以使用[`storage.indexBuildRetry`](../reference/configuration-options.html#storage.indexBuildRetry "storage.indexBuildRetry")配置文件设置或写入命令行参数[`--noIndexBuildRetry`](../reference/program/mongod.html#cmdoption-mongod-noindexbuildretry)。

MONGODB 4.0+
MONGODB 4.0以上版本

You cannot specify  [`storage.indexBuildRetry`](../reference/configuration-options.html#storage.indexBuildRetry "storage.indexBuildRetry")  or  [`--noIndexBuildRetry`](../reference/program/mongod.html#cmdoption-mongod-noindexbuildretry)  for a  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  that is part of a replica set.
你不能对副本集中的一个[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程实例，设置[`storage.indexBuildRetry`](../reference/configuration-options.html#storage.indexBuildRetry "storage.indexBuildRetry")选项  或  [`--noIndexBuildRetry`](../reference/program/mongod.html#cmdoption-mongod-noindexbuildretry)选项。

### Rollbacks during Build Process[](#rollbacks-during-build-process "Permalink to this headline")   构建过程中的回滚

Starting in version 4.0, MongoDB waits for any in-progress index builds to finish before starting a  [rollback](replica-set-rollbacks.html).
从4.0版本开始，MongoDB在任意正在执行索引构建的进程完成后进行[rollback](replica-set-rollbacks.html)。

## Monitor In Progress Index Builds[](#monitor-in-progress-index-builds "Permalink to this headline")  监视正在进行的索引生成

To see the status of an index build operation, you can use the  [`db.currentOp()`](../reference/method/db.currentOp.html#db.currentOp "db.currentOp()")  method in the  [`mongo`](../reference/program/mongo.html#bin.mongo "bin.mongo")  shell. To filter the current operations for index creation operations, see  [Active Indexing Operations](../reference/method/db.currentOp.html#currentop-index-creation)  for an example.
你可以在[`mongo`](../reference/program/mongo.html#bin.mongo "bin.mongo")  shell中执行[`db.currentOp()`](../reference/method/db.currentOp.html#db.currentOp "db.currentOp()")命令，查看索引构建操作的状态。筛选当前操作中的索引创建操作，参见[Active Indexing Operations](../reference/method/db.currentOp.html#currentop-index-creation)操作。

The  [`msg`](../reference/command/currentOp.html#currentOp.msg "currentOp.msg")  field includes a percentage-complete measurement of the current stage in the index build process.
[`msg`](../reference/command/currentOp.html#currentOp.msg "currentOp.msg")包含了索引构建当前阶段的完成百分比。

## Terminate In Progress Index Builds[](#terminate-in-progress-index-builds "Permalink to this headline")    终止正在进行的索引生成

To terminate an ongoing index build on a primary or standalone  `mongod`, use the  [`db.killOp()`](../reference/method/db.killOp.html#db.killOp "db.killOp()")  method in the  [`mongo`](../reference/program/mongo.html#bin.mongo "bin.mongo")  shell. When terminating an index build, the effects of  [`db.killOp()`](../reference/method/db.killOp.html#db.killOp "db.killOp()")  may not be immediate and may occur well after much of the index build operation has completed.
终止主节点或单个  `mongod` 进程中正在执行的索引构建命令，请在[`mongo`](../reference/program/mongo.html#bin.mongo "bin.mongo")进程中使用[`db.killOp()`](../reference/method/db.killOp.html#db.killOp "db.killOp()")命令。当终止索引生成时，[`db.killOp()`](../reference/method/db.killOp.html#db.killOp "db.killOp()")命令可能不会立即执行，并可能在大部分索引生成操作完成后执行。

You cannot terminate a  _replicated_  index build on secondary members of a replica set. You must first  [`drop`](../reference/method/db.collection.dropIndex.html#db.collection.dropIndex "db.collection.dropIndex()")  the index on the primary. The secondaries will replicate the drop operation and drop the indexes  _after_  the index build completes. All further replication blocks behind the index build and drop.
你无法终止一个已经在复制集从节点进行复制的索引构建操作。你必须首先在主节点[`drop`](../reference/method/db.collection.dropIndex.html#db.collection.dropIndex "db.collection.dropIndex()")删除索引。从节点将复制删除操作并在索引构建完成后删除索引。所用复制操作将阻塞直到索引构建完成并执行完删除操作。

To minimize the impact of building an index on replica sets and sharded clusters with replica set shards, see:
尽量降低复制集和拥有复制集的分片集群的构建索引影响，参见：

-   [Build Indexes on Replica Sets](../tutorial/build-indexes-on-replica-sets.html)
-   [Build Indexes on Sharded Clusters](../tutorial/build-indexes-on-sharded-clusters.html)

## Index Build Process[](#index-build-process "Permalink to this headline")  索引生成过程


The following table describes each stage of the index build process:
如下表格描述索引构建过程中的每个阶段：

| Stage | Description |
|--|--|
| Lock | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  obtains an exclusive  `X`  lock on the the collection being indexed. This blocks all read and write operations on the collection, including the application of any replicated write operations or metadata commands that target the collection. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  does not yield this lock. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 进程获得索引集合的独占`X`锁。该集合的所有读写操作被阻塞，包括应用程序对该集合所有复制写操作或者元数据指令。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程不会释放独占锁。 |
| Initialization 初始化 | The [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") creates three data structures at this initial state:The initial index metadata entry.A temporary table (“side writes table”) that stores keys generated from writes to the collection being indexed during the build process.A temporary table (“constraint violation table”) for all documents that may cause a duplicate-key constraint violation. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在该初始状态创建三个数据结构：初始索引元数据项。一张临时表（"side writes table"），用来存储构建过程中对索引集合进行写入时生成的key。一张临时表(“constraint violation table”)，用于存储可能导致重复键约束冲突的所有文档。|
| Lock  | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  downgrades the exclusive  `X`  collection lock to an intent exclusive  `IX`  lock. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  periodically yields this lock to interleaving read and write operations.  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 进程将之前获取的独占锁`X`降级为意图独占锁`IX`，[`mongod`]进程周期性的释放该锁，允许进行交错读写操作。|
| Scan Collection 扫描集合 | For each document in the collection, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  generates a key for that document and dumps the key into an external sorter. If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters a duplicate key error while generating a key during the collection scan, it stores that key in the constraint violation table for later processing.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters any other error while generating a key, the build fails with an error.Once the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  completes the collection scan, it dumps the sorted keys into the index. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程对集合中每个文档生成一个key，并将该key存储到外部分类器中，如果 [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在集合扫描期间发现重复key，它会将该key存储在constraint violation表中以备后续处理。如果mongod进程在生成key时遇到任何其他错误，构建将失败并出现错误。一旦mongod进程完成集合扫描，它将分类的key转储到索引中。|
| Process Side Writes Table 处理临时表Side Writes Table | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  drains the side write table using first-in-first-out priority.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters a duplicate key error while processing a key in the side write table, it stores that key in the constraint violation table for later processing.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters any other error while processing a key, the build fails with an error.For each document written to the collection during the build process, the  [`mongod`]../reference/program/mongod.html#bin.mongod "bin.mongod")  generates a key for that document and stores it in the side write table for later processing. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  uses a snapshot system to set a limit to the number of keys to process. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod") 使用先进先出方式处理Side Writes Table表中数据，如果遇到重复key，将该key写入constraint violation table表以备后续处理。如果[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在处理键时出现任何异常错误，构建将失败。对于每个在索引构建过程中写入集合的文档，mongod进程都会给该文档生成一个key，并将其存储到side write table表中。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")使用快照系统设置要处理的key的数量限制。|
| Lock | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  upgrades the intent exclusive  `IX`  lock on the collection to a shared  `S`  lock. This blocks all write operations to the collection, including the application of any replicated write operations or metadata commands that target the collection. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程将索引集合上的意图独占锁`IX`升级为共享`S`锁。这会阻塞该集合的所有写操作，包括应用程序对该集合的任何复制写操作或元数据操作。|
| Finish Processing Temporary Side Writes Table 完成对Temporary Side Writes Table表的处理 | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  continues draining remaining records in the side writes table. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  may pause replication during this stage.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters a duplicate key error while processing a key in the side write table, it stores that key in the constraint violation table for later processing.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters any other error while processing a key, the build fails with an error.[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程继续处理Side Writes Table表中的存量数据。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在该阶段可能会暂停复制。如果遇到重复key，将该key写入constraint violation table表以备后续处理。如果[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在处理键时出现任何异常错误，构建将失败。 |
| Lock  | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  upgrades the shared  `S`  lock on the collection to an exclusive  `X`  lock on the collection. This blocks all read and write operations on the collection, including the application of any replicated write operations or metadata commands that target the collection. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  does not yield this lock. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程将索引集合的共享`S`锁升级成独占`X`锁。该集合的所有读写操作被阻塞，包括应用程序对该集合所有复制写操作或者元数据指令操作。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程不会释放独占锁。 |
| Drop Side Write Table 删除Side Write Table表| The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  applies any remaining operations in the side writes table before dropping it.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters a duplicate key error while processing a key in the side write table, it stores that key in the constraint violation table for later processing.If the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  encounters any other error while processing a key, the build fails with an error.At this point, the index includes all data written to the collection. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")在处理完Side Write Table表中所有数据后将其删除。如果遇到重复key，将该key写入constraint violation table表以备后续处理。如果[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在处理键时出现任何异常错误，构建将失败。|
| Process Constraint Violation Table  处理Constraint Violation Table表 | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  drains the constraint violation table using first-in-first-out priority. The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  then drops the table.If any key in the constraint violation table still produces a duplicate key error, the  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  aborts the build and throws an error.The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  drops the constraint violation table once it is drained  _or_  if it encounters a duplicate key violation during processing. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程使用先入先出的方式处理Constraint Violation Table表中数据，之后删除该表。如果其中任何键仍然出现重复键错误，[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")将终止构建并抛出错误。[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程在处理完Constraint Violation Table表后或出现重复键约束时将删除该表。|
| Mark the Index as Ready 标记索引已就绪| The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  updates the index metadata to mark the index as ready for use.[`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")将索引元数据更新为可使用状态。 |
| Lock | The  [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")  releases the  `X`  lock on the collection. [`mongod`](../reference/program/mongod.html#bin.mongod "bin.mongod")进程释放该索引集合的独占`X`锁。|



原文链接：https://docs.mongodb.com/manual/core/index-creation/

译者：程哲欣

