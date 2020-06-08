# Measure Index Use[](https://docs.mongodb.com/manual/tutorial/measure-index-use/#measure-index-use "Permalink to this headline")  度量索引使用情况

On this page

-   [Get Index Access Information with  `$indexStats`](https://docs.mongodb.com/manual/tutorial/measure-index-use/#get-index-access-information-with-indexstats)    使用$indexStats度量索引使用
-   [Return Query Plan with  `explain()`](https://docs.mongodb.com/manual/tutorial/measure-index-use/#return-query-plan-with-explain)  使用 `explain()`返回查询计划
-   [Control Index Use with  `hint()`](https://docs.mongodb.com/manual/tutorial/measure-index-use/#control-index-use-with-hint)    使用`hint()`控制索引使用
-   [Index Metrics](https://docs.mongodb.com/manual/tutorial/measure-index-use/#index-metrics)    其他索引度量方法

## Get Index Access Information with  `$indexStats`[](https://docs.mongodb.com/manual/tutorial/measure-index-use/#get-index-access-information-with-indexstats "Permalink to this headline")    使用$indexStats度量索引使用


Use the  [`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats "$indexStats")  aggregation stage to get statistics regarding the use of each index for a collection. For example, the following aggregation operation returns statistics on the index use on the  `orders`  
collection:
在aggregate操作时使用[`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats "$indexStats") 参数获取集合的每个索引使用情况的统计信息。例如：如下语句将返回`orders`表的索引使用统计信息

    db.orders.aggregate( [ { $indexStats: { } } ] )

SEE ALSO
也可参考：

[`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats "$indexStats")

## Return Query Plan with  `explain()`[](https://docs.mongodb.com/manual/tutorial/measure-index-use/#return-query-plan-with-explain "Permalink to this headline")    使用 `explain()`返回查询计划

Use the  [`db.collection.explain()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()")  or the  [`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()")  method in  [executionStats](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#explain-method-executionstats)  mode to return statistics about the query process, including the index used, the number of documents scanned, and the time the query takes to process in milliseconds.
在[executionStats](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#explain-method-executionstats) 模式下使用 [`db.collection.explain()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()") 或[`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()")方法返回语句查询过程的统计信息，包括索引使用，文档遍历数量，和查询处理所需的时间。

Run  [`db.collection.explain()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()")  or the  [`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()")  method in  [allPlansExecution](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#explain-method-allplansexecution)  mode to view partial execution statistics collected during plan selection.
在[allPlansExecution](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#explain-method-allplansexecution) 模式下使用 [`db.collection.explain()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()") 或[`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()")方法查看计划选择期间收集的部分执行统计信息。

SEE ALSO
也可参考：

[planCacheKey](https://docs.mongodb.com/manual/core/query-plans/#plan-cache-key)

## Control Index Use with  `hint()`[](https://docs.mongodb.com/manual/tutorial/measure-index-use/#control-index-use-with-hint "Permalink to this headline")    使用`hint()`控制索引使用

To  _force_  MongoDB to use a particular index for a  [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")  operation, specify the index with the[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")  method. Append the  [`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")  method to the  [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")  method. Consider the following example:
强制MongoDB在执行[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")相关命令时使用特定索引，在[`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")方法后追加[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")方法，并将特定索引在[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")方法中声明。
代码示例如下：

    db.people.find(
       { name: "John Doe", zipcode: { $gt: "63000" } }
    ).hint( { zipcode: 1 } )

To view the execution statistics for a specific index, append to the  [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")  the  [`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")method followed by  [`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()"), e.g.:
查看使用特定索引的执行统计信息，在[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find "db.collection.find()")语句追加的[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")方法后跟随[`cursor.explain()`](https://docs.mongodb.com/manual/reference/method/cursor.explain/#cursor.explain "cursor.explain()")方法，代码示例如下：

    db.people.find(
       { name: "John Doe", zipcode: { $gt: "63000" } }
    ).hint( { zipcode: 1 } ).explain("executionStats")

Or, append  [`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")  method to  [`db.collection.explain().find()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()"):
或者在[`db.collection.explain().find()`](https://docs.mongodb.com/manual/reference/method/db.collection.explain/#db.collection.explain "db.collection.explain()")方法后追加[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")方法。

    db.people.explain("executionStats").find(
       { name: "John Doe", zipcode: { $gt: "63000" } }
    ).hint( { zipcode: 1 } )

Specify the  `$natural`  operator to the  [`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")  method to prevent MongoDB from using  _any_  index:
在[`hint()`](https://docs.mongodb.com/manual/reference/method/cursor.hint/#cursor.hint "cursor.hint()")方法中声明`$natural`参数，避免MongoDB在查询过程中使用任何索引。

    db.people.find(
       { name: "John Doe", zipcode: { $gt: "63000" } }
    ).hint( { $natural: 1 } )

## Index Metrics[](https://docs.mongodb.com/manual/tutorial/measure-index-use/#index-metrics "Permalink to this headline")    其他索引度量方法

In addition to the  [`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats "$indexStats")  aggregation stage, MongoDB provides various index statistics that you may want to consider when analyzing index use for your database:
当你在分析数据的索引使用情况时，除了在aggregate过程中声明[`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats "$indexStats")参数外，亦可使用如下几种方法统计索引使用。
|  |  |
|--|--|
| In the output of  [`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus "serverStatus"):
在[`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus "serverStatus")方法的输出结果中： | [`metrics.queryExecutor.scanned`](https://docs.mongodb.com/manual/reference/command/serverStatus/#serverstatus.metrics.queryExecutor.scanned "metrics.queryExecutor.scanned")和[`metrics.operation.scanAndOrder`](https://docs.mongodb.com/manual/reference/command/serverStatus/#serverstatus.metrics.operation.scanAndOrder "metrics.operation.scanAndOrder") |
| In the output of  [`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats "collStats"):在[`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats "collStats")输出结果 | [`totalIndexSize`](https://docs.mongodb.com/manual/reference/command/collStats/#collStats.totalIndexSize "collStats.totalIndexSize")和[`indexSizes`](https://docs.mongodb.com/manual/reference/command/collStats/#collStats.indexSizes "collStats.indexSizes") |
| In the output of  [`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats "dbStats"):在[`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats "dbStats")输出结果 | [`dbStats.indexes`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbStats.indexes "dbStats.indexes")和[`dbStats.indexSize`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbStats.indexSize "dbStats.indexSize") |



原文链接：https://docs.mongodb.com/manual/tutorial/measure-index-use/#measure-index-use

译者：程哲欣
