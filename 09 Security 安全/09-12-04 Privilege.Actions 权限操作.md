# Privilege Actions权限操作



On this page

- [Query and Write Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#query-and-write-actions)
- [查询和写入操作](https://docs.mongodb.com/manual/reference/privilege-actions/#query-and-write-actions)
- [Database Management Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#database-management-actions)
- [数据管理操作](https://docs.mongodb.com/manual/reference/privilege-actions/#database-management-actions)
- [Deployment Management Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#deployment-management-actions)
- [部署管理操作](https://docs.mongodb.com/manual/reference/privilege-actions/#deployment-management-actions)
- [Change Stream Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#change-stream-actions)
- [变更流操作](https://docs.mongodb.com/manual/reference/privilege-actions/#change-stream-actions)
- [Replication Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#replication-actions)
- [复制集操作](https://docs.mongodb.com/manual/reference/privilege-actions/#replication-actions)
- [Sharding Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#sharding-actions)
- [分片操作](https://docs.mongodb.com/manual/reference/privilege-actions/#sharding-actions)
- [Server Administration Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#server-administration-actions)
- [服务器管理操作](https://docs.mongodb.com/manual/reference/privilege-actions/#server-administration-actions)
- [Session Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#session-actions)
- [会话操作](https://docs.mongodb.com/manual/reference/privilege-actions/#session-actions)
- [Free Monitoring Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#free-monitoring-actions)
- [免费监控操作](https://docs.mongodb.com/manual/reference/privilege-actions/#free-monitoring-actions)
- [Diagnostic Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#diagnostic-actions)
- [诊断操作](https://docs.mongodb.com/manual/reference/privilege-actions/#diagnostic-actions)
- [Internal Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#internal-actions)
- [内部操作](https://docs.mongodb.com/manual/reference/privilege-actions/#internal-actions)

Privilege actions define the operations a user can perform on a [resource](https://docs.mongodb.com/manual/reference/resource-document/#resource-document). A MongoDB [privilege](https://docs.mongodb.com/manual/core/authorization/#privileges) comprises a [resource](https://docs.mongodb.com/manual/reference/resource-document/#resource-document) and the permitted actions. This page lists available actions grouped by common purpose.

权限操作定义了用户可以对[资源](https://docs.mongodb.com/manual/reference/resource-document/#resource-document)做的操作。一个 MongoDB [权限](https://docs.mongodb.com/manual/core/authorization/#privileges)包括[资源](https://docs.mongodb.com/manual/reference/resource-document/#resource-document)和允许的操作。本页面列出了按通用目的分组的可用操作。

MongoDB provides built-in roles with pre-defined pairings of resources and permitted actions. For lists of the actions granted, see [Built-In Roles](https://docs.mongodb.com/manual/reference/built-in-roles/). To define custom roles, see [Create a User-Defined Role](https://docs.mongodb.com/manual/tutorial/manage-users-and-roles/#create-user-defined-role).

MongoDB 为内置角色提供了预定义的资源和允许的操作。有关授予的操作的列表，请参阅 [内置角色](https://docs.mongodb.com/manual/reference/built-in-roles/)。要定义自定义角色，请参阅 [创建用户定义角色](https://docs.mongodb.com/manual/tutorial/manage-users-and-roles/#create-user-defined-role)。

## Query and Write Actions

## 查询和写操作

- `find`

  User can perform the following commands, and their equivalent helper methods:

  用户可以操作以下命令行，以及和他们等效的帮助方法：

  [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) for all [pipeline operations](https://docs.mongodb.com/manual/reference/operator/aggregation/) **except** [`$collStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/collStats/#pipe._S_collStats), [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out), and [`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats).

  [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) 用来对除了[`$collStats、`](https://docs.mongodb.com/manual/reference/operator/aggregation/collStats/#pipe._S_collStats)[`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out)和 [`$indexStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/#pipe._S_indexStats)的所有管道操作。

  [`checkShardingIndex`](https://docs.mongodb.com/manual/reference/command/checkShardingIndex/#dbcmd.checkShardingIndex)

  [`检查分片索引`](https://docs.mongodb.com/manual/reference/command/checkShardingIndex/#dbcmd.checkShardingIndex)

  [`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count)

  [`统计`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count)

  [`dataSize`](https://docs.mongodb.com/manual/reference/command/dataSize/#dbcmd.dataSize)

  [`数据大小`](https://docs.mongodb.com/manual/reference/command/dataSize/#dbcmd.dataSize)

  [`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct)

  [` 取唯一值 `](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct)

  [`filemd5`](https://docs.mongodb.com/manual/reference/command/filemd5/#dbcmd.filemd5)

  [`文件 md5`](https://docs.mongodb.com/manual/reference/command/filemd5/#dbcmd.filemd5)

  [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)

  [` 查找 `](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)

  [`geoSearch`](https://docs.mongodb.com/manual/reference/command/geoSearch/#dbcmd.geoSearch)

  [`地理位置查找`](https://docs.mongodb.com/manual/reference/command/geoSearch/#dbcmd.geoSearch)

  [`getLastError`](https://docs.mongodb.com/manual/reference/command/getLastError/#dbcmd.getLastError)

  [`获得最后错误`](https://docs.mongodb.com/manual/reference/command/getLastError/#dbcmd.getLastError)

  [`getMore`](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore)

  [`获得更多`](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore)

  [`killCursors`](https://docs.mongodb.com/manual/reference/command/killCursors/#dbcmd.killCursors), provided that the cursor is associated with a currently authenticated user.

  提供与当前经过身份验证的用户相关联的光标

  [`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections)

  [`列出所有集合`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections)

  [`listIndexes`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)

  [`列出索引`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)

  [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) with the `{out: inline}` option. 

  [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce)和选项 `{out: inline}` 一起使用。

  [`resetError`](https://docs.mongodb.com/manual/reference/command/resetError/#dbcmd.resetError)
  [` 重置错误 `](https://docs.mongodb.com/manual/reference/command/resetError/#dbcmd.resetError)

  Required for the query portion of the [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) command and [`db.collection.mapReduce`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) helper method when [outputting to a collection](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd).

  [输出到集合](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd)时，[`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) 命令和 [`db.collection.mapReduce`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) 辅助方法的查询部分是必需的。

  Required for the query portion of the [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) command and [`db.collection.findAndModify`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify) helper method.

  [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)命令和[`db.collection.findAndModify`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)辅助方法的查询部分是必需的。

  Required on the *source* collection for the [`cloneCollectionAsCapped`](https://docs.mongodb.com/manual/reference/command/cloneCollectionAsCapped/#dbcmd.cloneCollectionAsCapped) and [`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection) commands and the [`db.collection.renameCollection()`](https://docs.mongodb.com/manual/reference/method/db.collection.renameCollection/#db.collection.renameCollection) helper method.

  [`cloneCollectionAsCapped`](https://docs.mongodb.com/manual/reference/command/cloneCollectionAsCapped/#dbcmd.cloneCollectionAsCapped) 和 [`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection)命令行和 [`db.collection.renameCollection()`](https://docs.mongodb.com/manual/reference/method/db.collection.renameCollection/#db.collection.renameCollection)辅助方法要求有源集合。

  For MongoDB 4.0.6+:

  If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has privileges (including databases for which the user has privileges on specific collections) if the command is run with `authorizedDatabases` option unspecified or set to `true`.

  对于 MongoDB 4.0.6+：

  如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作， 用户运行[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) 命令行时`authorizedDatabases`选项未指定或设置为 true，则用户可以运行该命令以返回该用户具有权限的数据库的列表（包括该用户对其特定集合具有特权的数据库）。

  For MongoDB 4.0.5:

  If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has the [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) action privilege if the command is run with `authorizedDatabases` option unspecified or set to `true`.

  对于 MongoDB 4.0.5:

  如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作，在authorizedDatabases未指定或设置为true的情况下运行 [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) 命令时，用户可以运行该命令以返回用户对其具有[`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)操作权限的数据库列表 。

- For MongoDB 4.0.0-4.0.4:

- If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has the [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) action privilege.Apply this action to database or collection resources.

- 对于MongoDB 4.0.0-4.0.4:

- 如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作，则用户可以运行[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) 命令以返回该用户对其具有[`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)操作权限的数据库列表 。

- `insert`  

  User can perform the following commands and their equivalent methods:

  用户可以执行以下命令行和他们的等价方法

  [`insert`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert)

  [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)

  Required for the output portion of the [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) command and [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) helper method when [outputting to a collection](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd).

  当要[输出到集合](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd)时，[`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) 命令和 [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce)方法的 output 部分是必须的。

  Required for the [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) command and [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) helper method when using the [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) pipeline operator.

  使用管道运算符时，[`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)命令和 [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)帮助程序方法必需[`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out)。

  Required for the [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) and [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) commands and equivalent helper methods when used with the `upsert` option.

  当时使用 [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) 和 [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) 命令行 和 等价方法是，upsert 选项必须。

  Required on the *destination* collection for the following commands and their helper methods:

  以下命令及其辅助方法在*目标*集合上是必需的：

  [`cloneCollection`](https://docs.mongodb.com/manual/reference/command/cloneCollection/#dbcmd.cloneCollection)

  [`克隆集合`](https://docs.mongodb.com/manual/reference/command/cloneCollection/#dbcmd.cloneCollection)

  [`cloneCollectionAsCapped`](https://docs.mongodb.com/manual/reference/command/cloneCollectionAsCapped/#dbcmd.cloneCollectionAsCapped)

  [`克隆集合为上限集合`](https://docs.mongodb.com/manual/reference/command/cloneCollectionAsCapped/#dbcmd.cloneCollectionAsCapped)

  [`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection)

  [`重命名集合`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection)

  Apply this action to database or collection resources.

  将此操作应用于数据库或集合资源。

- `remove`

  User can perform the [`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete) command and equivalent helper method.Required for the write portion of the [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) command and [`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify) method.Required for the [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) command and [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) helper method when you specify the `replace` action when [outputting to a collection](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd).Required for the [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) command and [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) helper method when using the [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) pipeline operator.Apply this action to database or collection resources.

  用户可以执行 [`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete) 命令和等效的辅助方法。[`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) 命令和[`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)方法的 write 部分必需。当您指定`replace` [输出到集合](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd)时，该 [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) 命令和 [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce)辅助方法是必需的。使用 [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) 管道运算符时，[`aggregate `](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)命令和 [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) 帮助程序方法是必需的。将此操作应用于数据库或集合资源。

- `update`

  User can perform the [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) command and equivalent helper methods.Required for the [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) command and [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) helper method when [outputting to a collection](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd) without specifying the `replace` action.Required for the [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) command and [`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify) helper method.Apply this action to database or collection resources.

  用户可以执行[`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) 命令和等效的帮助方法。在不指定`replace`情况下操作[输出到集合](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#mapreduce-out-mtd)时，[`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) 命令和 [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce)帮助方法是必需的 。[`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)命令和 [`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)帮助程序方法必需。

  将此操作应用于数据库或集合资源。

- `bypassDocumentValidation`

  *New in version 3.2.*

  Users can bypass [document validation](https://docs.mongodb.com/manual/core/schema-validation/) on commands and methods that support the `bypassDocumentValidation` option. The following commands and their equivalent methods support bypassing document validation:

  *3.2版新功能* 。

  用户可以绕过支持该选项的命令和方法的[文档验证](https://docs.mongodb.com/manual/core/schema-validation/)`bypassDocumentValidation`。以下命令及其等效方法支持绕过文档验证：

  [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)

  [`管道操作`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)

  [`applyOps`](https://docs.mongodb.com/manual/reference/command/applyOps/#dbcmd.applyOps)

  [` 应用操作 `](https://docs.mongodb.com/manual/reference/command/applyOps/#dbcmd.applyOps)

  [`cloneCollection`](https://docs.mongodb.com/manual/reference/command/cloneCollection/#dbcmd.cloneCollection) on the *destination* collection 

  在*目的地*集合上[` 克隆集合 `](https://docs.mongodb.com/manual/reference/command/cloneCollection/#dbcmd.cloneCollection)

  [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)

  [`查找并修改`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)

  [`insert`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert)

  [`插入`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert)

  [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce)

  [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update)

  [`更新`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update)

  Apply this action to database or collection resources.

  将此操作应用于数据库或集合资源。

- `useUUID`

  *New in version 3.6.*

  *3.6版的新功能。*

  User can execute the following commands using a UUID as if it were a namespace:

  用户可以使用UUID来执行以下命令 ，就像它是名称空间一样：

  [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)

  [`查找`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)

  [`listIndexes`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)

  [`列出索引`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)

  For example, this privilege authorizes a user to run the following command which executes a [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) command on a collection with the given UUID. In order to be successful, this operation also requires that the user is authorized to execute the `find` command on the collection namespace corresponding to the given UUID.

  copy

  ```
db.runCommand({find: UUID("123e4567-e89b-12d3-a456-426655440000")})
  ```
  
  For more information on collection UUIDs, see [Collections](https://docs.mongodb.com/manual/core/databases-and-collections/#collections).Apply this action to the `cluster` resource.

  例如，此特权授权用户运行以下命令，该[`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)命令对具有给定UUID的集合执行命令。为了获得成功，此操作还需要授权用户`find`在与给定UUID对应的集合名称空间上执行 命令。

  例如，此特权授权用户运行以下命令，该[`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)命令对具有给定UUID的集合执行命令。为了获得成功，此操作还需要授权用户`find`在与给定UUID对应的集合名称空间上执行 命令。
  
  复制
  
  ```
  db.runCommand({ find:UUID(“123e4567-e89b-12d3-a456-426655440000”)})
  ```
  
  有关集合UUID的更多信息，请参见 [集合](https://docs.mongodb.com/manual/core/databases-and-collections/#collections)。
  
  将此操作应用于`cluster`资源。

## Database Management Actions 

## 数据库管理操作

- `changeCustomData`

  User can change the custom information of any user in the given database. Apply this action to database resources.

  用户可以更改给定数据库中任何用户的自定义信息。将此操作应用于数据库资源。

- `changeOwnCustomData`

  Users can change their own custom information. Apply this action to database resources. See also [Change Your Password and Custom Data](https://docs.mongodb.com/manual/tutorial/change-own-password-and-custom-data/).

  用户可以更改自己自定义的信息。将此操作应用于数据库资源。另请参阅 [更改密码和自定义数据](https://docs.mongodb.com/manual/tutorial/change-own-password-and-custom-data/)。

- `changeOwnPassword`

  Users can change their own passwords. Apply this action to database resources. See also [Change Your Password and Custom Data](https://docs.mongodb.com/manual/tutorial/change-own-password-and-custom-data/).

  用户可以更改自己的密码。将此操作应用于数据库资源。另请参阅 [更改密码和自定义数据](https://docs.mongodb.com/manual/tutorial/change-own-password-and-custom-data/)。

- `changePassword`

  User can change the password of any user in the given database. Apply this action to database resources.

  用户可以更改给定数据库中任何用户的密码。将此操作应用于数据库资源。

- `createCollection`

  User can perform the [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) method. Apply this action to database or collection resources.

  用户可以执行该[`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)方法。将此操作应用于数据库或集合资源。

- `createIndex`

  Provides access to the [`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) method and the [`createIndexes`](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes) command. Apply this action to database or collection resources.

  提供对[`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)方法和[`createIndexes`](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes)命令的访问。将此操作应用于数据库或集合资源。

- `createRole`

  User can create new roles in the given database. Apply this action to database resources.

  用户可以在给定的数据库中创建新角色。将此操作应用于数据库资源。

- `createUser`

  User can create new users in the given database. Apply this action to database resources.

  用户可以在给定的数据库中创建新用户。将此操作应用于数据库资源。

- `dropCollection`

  User can perform the [`db.collection.drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop) method. Apply this action to database or collection resources.

  用户可以执行该[`db.collection.drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop)方法。将此操作应用于数据库或集合资源。

- `dropRole`

  User can delete any role from the given database. Apply this action to database resources.

  用户可以从给定的数据库中删除任何角色。将此操作应用于数据库资源。

- `dropUser`

  User can remove any user from the given database. Apply this action to database resources.

  用户可以从给定数据库中删除任何用户。将此操作应用于数据库资源。

- `enableProfiler`

  User can perform the [`db.setProfilingLevel()`](https://docs.mongodb.com/manual/reference/method/db.setProfilingLevel/#db.setProfilingLevel) method. Apply this action to database resources.

  用户可以执行该[`db.setProfilingLevel()`](https://docs.mongodb.com/manual/reference/method/db.setProfilingLevel/#db.setProfilingLevel)方法。将此操作应用于数据库资源。

- `grantRole`

  User can grant any role in the database to any user from any database in the system. Apply this action to database resources.

  用户可以将数据库中的任何角色从系统中的任何数据库授予任何用户。将此操作应用于数据库资源。

- `killCursors`

  Starting in MongoDB 4.2, users can always kill their own cursors, regardless of whether the users have the privilege to [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors). As such, the [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) privilege has no effect in MongoDB 4.2+.In MongoDB 3.6.3 through MongoDB 4.0.x, users require [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) privilege to kill their own curors when access control is enabled. Cursors are associated with the users at the time of cursor creation. Apply this action to collection resources.

  从MongoDB 4.2开始，用户始终可以杀死自己的游标，而不管用户是否具有 [`killCursors `](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors)的权限。因此，该[`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) 特权在MongoDB 4.2+版本中无效。在MongoDB 3.6.3到MongoDB 4.0.x中，[`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors)启用访问控制后，用户需要 特权才能杀死自己的Curor。游标创建时，游标与用户相关联。将此操作应用于收集资源。

- `killAnyCursor`

  *New in version 3.6.3.*User can kill **any** cursor, even cursors created by other users. Apply this action to collection resources.

  版本3.6.3中的新功能。

  用户可以杀死**任何**游标，甚至可以杀死其他用户创建的游标。将此操作应用于收集资源。

- `revokeRole`

  User can remove any role from any user from any database in the system. Apply this action to database resources.

  用户可以从系统中任何数据库的任何用户中删除任何角色。将此操作应用于数据库资源。

- `setAuthenticationRestriction`

  New in version 3.6. 

  User can specify the [authenticationRestrictions](https://docs.mongodb.com/manual/reference/command/createUser/#create-user-auth-restrictions) field in the `user` document when running the following commands:

  3.6版的新功能

  运行以下命令时，用户可以在文档中指定 [authenticationRestrictions](https://docs.mongodb.com/manual/reference/command/createUser/#create-user-auth-restrictions)字段`user`：

  [createUser](https://docs.mongodb.com/manual/reference/command/createUser/)

  [创建用户](https://docs.mongodb.com/manual/reference/command/createUser/)

  [updateUser](https://docs.mongodb.com/manual/reference/command/updateUser/)

  [更新用户](https://docs.mongodb.com/manual/reference/command/updateUser/)

  User can specify the `authenticationRestrictions` field in the `role` document when running the following commands:

  运行以下命令时，用户可以`authenticationRestrictions`在`role`文档中指定字段 ：

  [createRole](https://docs.mongodb.com/manual/reference/command/createRole/)

  [创建角色](https://docs.mongodb.com/manual/reference/command/createRole/)

  [updateRole](https://docs.mongodb.com/manual/reference/command/updateRole/)

  [更新角色](https://docs.mongodb.com/manual/reference/command/updateRole/)

  NOTE

  注意

  The following built-in roles grant this privilege:

  The [`userAdmin`](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdmin) role provides this privilege on the database that the role is assigned.

  The [`userAdminAnyDatabase`](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdminAnyDatabase) role provides this privilege on all databases.

  Transitively, the [`restore`](https://docs.mongodb.com/manual/reference/built-in-roles/#restore) and [`root`](https://docs.mongodb.com/manual/reference/built-in-roles/#root) roles also provide this privilege.Apply this action to database resources.

  以下内置角色授予此特权：

  该[`userAdmin`](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdmin)角色提供对数据库的这一特权的角色分配。

  该 [`userAdminAnyDatabase`](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdminAnyDatabase)角色在所有数据库上提供此特权。

  可传递地，[`restore`](https://docs.mongodb.com/manual/reference/built-in-roles/#restore)和[`root`](https://docs.mongodb.com/manual/reference/built-in-roles/#root)角色也提供此特权。将此操作应用于数据库资源。

- `unlock`

  User can perform the [`db.fsyncUnlock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncUnlock/#db.fsyncUnlock) method. Apply this action to the `cluster` resource.

  用户可以执行该[`db.fsyncUnlock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncUnlock/#db.fsyncUnlock)方法。将此操作应用于`cluster`资源。

- `viewRole`

  User can view information about any role in the given database. Apply this action to database resources.

  用户可以查看有关给定数据库中任何角色的信息。将此操作应用于数据库资源。

- `viewUser`

  User can view the information of any user in the given database. Apply this action to database resources.
  
  用户可以在给定的数据库中查看任何用户的信息。将此操作应用于数据库资源。

## Deployment Management Actions 

## 部署管理操作

- `authSchemaUpgrade`

  User can perform the `authSchemaUpgrade` command. Apply this action to the `cluster` resource.

  用户可以执行`authSchemaUpgrade`命令。将此操作应用于`cluster`资源。

- `cleanupOrphaned`

  User can perform the [`cleanupOrphaned`](https://docs.mongodb.com/manual/reference/command/cleanupOrphaned/#dbcmd.cleanupOrphaned) command. Apply this action to the `cluster` resource.

  用户可以执行[`cleanupOrphaned`](https://docs.mongodb.com/manual/reference/command/cleanupOrphaned/#dbcmd.cleanupOrphaned)命令。将此操作应用于`cluster`资源。

- `cpuProfiler`

  User can enable and use the CPU profiler. Apply this action to the `cluster` resource.

  用户可以启用和使用CPU Profiler。将此操作应用于 `cluster`资源。

- `inprog`

  User can use the [`db.currentOp()`](https://docs.mongodb.com/manual/reference/method/db.currentOp/#db.currentOp) method to return information on pending and active operations. Apply this action to the `cluster` resource.

  *Changed in version 3.2.9:* Even without the [`inprog`](https://docs.mongodb.com/manual/reference/privilege-actions/#inprog) privilege, on [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances, users can view their own operations by running `db.currentOp( { "$ownOps": true } )`.

  用户可以使用该[`db.currentOp()`](https://docs.mongodb.com/manual/reference/method/db.currentOp/#db.currentOp)方法返回有关挂起和活动操作的信息。将此操作应用于`cluster`资源。

  *在版本3.2.9中进行了更改：*即使没有[`inprog`](https://docs.mongodb.com/manual/reference/privilege-actions/#inprog)特权[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)，用户也可以在实例上通过运行`db.currentOp( { "$ownOps": true } )`来查看自己的操作。

- `invalidateUserCache`

  Provides access to the [`invalidateUserCache`](https://docs.mongodb.com/manual/reference/command/invalidateUserCache/#dbcmd.invalidateUserCache) command. Apply this action to the `cluster` resource.

  提供对[`invalidateUserCache`](https://docs.mongodb.com/manual/reference/command/invalidateUserCache/#dbcmd.invalidateUserCache)命令的访问。将此操作应用于`cluster`资源。

- `killop`

  User can perform the [`db.killOp()`](https://docs.mongodb.com/manual/reference/method/db.killOp/#db.killOp) method. Apply this action to the `cluster` resource.

  用户可以执行该[`db.killOp()`](https://docs.mongodb.com/manual/reference/method/db.killOp/#db.killOp)方法。将此操作应用于`cluster`资源。

  *Changed in version 3.2.9:* Even without the [`killop`](https://docs.mongodb.com/manual/reference/privilege-actions/#killop) privilege, on [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instances, users can kill their own operations.

  *在版本3.2.9中更改：*即使没有[`killop`](https://docs.mongodb.com/manual/reference/privilege-actions/#killop)特权，在 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)实例上，用户也可以杀死自己的操作。

- `planCacheRead`

  User can run the following operations:

  [`$planCacheStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/planCacheStats/#pipe._S_planCacheStats) aggregation stage.

  [`planCacheListPlans`](https://docs.mongodb.com/manual/reference/command/planCacheListPlans/#dbcmd.planCacheListPlans) command and [`PlanCache.getPlansByQuery()`](https://docs.mongodb.com/manual/reference/method/PlanCache.getPlansByQuery/#PlanCache.getPlansByQuery) method.

  [`planCacheListQueryShapes`](https://docs.mongodb.com/manual/reference/command/planCacheListQueryShapes/#dbcmd.planCacheListQueryShapes) command and the [`PlanCache.listQueryShapes()`](https://docs.mongodb.com/manual/reference/method/PlanCache.listQueryShapes/#PlanCache.listQueryShapes) method.

  Apply this action to database or collection resources.

  

  用户可以执行以下操作：

  - [`$planCacheStats`](https://docs.mongodb.com/manual/reference/operator/aggregation/planCacheStats/#pipe._S_planCacheStats) 聚集阶段。
  - [`planCacheListPlans`](https://docs.mongodb.com/manual/reference/command/planCacheListPlans/#dbcmd.planCacheListPlans)命令和 [`PlanCache.getPlansByQuery()`](https://docs.mongodb.com/manual/reference/method/PlanCache.getPlansByQuery/#PlanCache.getPlansByQuery)方法。
  - [`planCacheListQueryShapes`](https://docs.mongodb.com/manual/reference/command/planCacheListQueryShapes/#dbcmd.planCacheListQueryShapes)命令和 [`PlanCache.listQueryShapes()`](https://docs.mongodb.com/manual/reference/method/PlanCache.listQueryShapes/#PlanCache.listQueryShapes)方法。

  将此操作应用于数据库或集合资源。

- `planCacheWrite`

  User can perform the [`planCacheClear`](https://docs.mongodb.com/manual/reference/command/planCacheClear/#dbcmd.planCacheClear) command and the [`PlanCache.clear()`](https://docs.mongodb.com/manual/reference/method/PlanCache.clear/#PlanCache.clear) and [`PlanCache.clearPlansByQuery()`](https://docs.mongodb.com/manual/reference/method/PlanCache.clearPlansByQuery/#PlanCache.clearPlansByQuery) methods. Apply this action to database or collection resources.

  用户可以执行[`planCacheClear`](https://docs.mongodb.com/manual/reference/command/planCacheClear/#dbcmd.planCacheClear)命令以及 [`PlanCache.clear()`](https://docs.mongodb.com/manual/reference/method/PlanCache.clear/#PlanCache.clear)和[`PlanCache.clearPlansByQuery()`](https://docs.mongodb.com/manual/reference/method/PlanCache.clearPlansByQuery/#PlanCache.clearPlansByQuery) 方法。将此操作应用于数据库或集合资源。

- `storageDetails`

  User can perform the `storageDetails` command. Apply this action to database or collection resources.
  
  用户可以执行`storageDetails`命令。将此操作应用于数据库或集合资源。

## Change Stream Actions 

## 变更流操作

- `changeStream`

  User with [`changeStream`](https://docs.mongodb.com/manual/reference/privilege-actions/#changeStream) and [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) on the specific collection, all non-`system` collections in a specifc database, or all non-`system` collections across all databases can open [change stream cursor](https://docs.mongodb.com/manual/changeStreams/) for that resource.
  
  用户在指定集合，或在指定数据库中的全体非system集合，或在所有数据库中的所有非system集合中有[`changeStream`](https://docs.mongodb.com/manual/reference/privilege-actions/#changeStream) 和 [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)权限，那么该用户可以为这些资源打开[变更流指针](https://docs.mongodb.com/manual/changeStreams/) 

## Replication Actions 

## 副本集操作

- `appendOplogNote`

  User can append notes to the oplog. Apply this action to the `cluster` resource.

  用户可以在操作日志中添加注释。 将此动作应用于`cluster`资源。

- `replSetConfigure`

  User can configure a replica set. Apply this action to the `cluster` resource.

  用户可以在操作日志中添加注释。将此操作应用于 `cluster`资源。

- `replSetGetConfig`

  User can view a replica set’s configuration. Provides access to the [`replSetGetConfig`](https://docs.mongodb.com/manual/reference/command/replSetGetConfig/#dbcmd.replSetGetConfig) command and [`rs.conf()`](https://docs.mongodb.com/manual/reference/method/rs.conf/#rs.conf) helper method.Apply this action to the `cluster` resource.

  用户可以查看副本集的配置。提供对[`replSetGetConfig`](https://docs.mongodb.com/manual/reference/command/replSetGetConfig/#dbcmd.replSetGetConfig)命令和[`rs.conf()`](https://docs.mongodb.com/manual/reference/method/rs.conf/#rs.conf)辅助方法的访问 。

  将此操作应用于`cluster`资源。

- `replSetGetStatus`

  User can perform the [`replSetGetStatus`](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#dbcmd.replSetGetStatus) command. Apply this action to the `cluster` resource.

  用户可以执行[`replSetGetStatus`](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#dbcmd.replSetGetStatus)命令。将此操作应用于`cluster`资源。

- `replSetHeartbeat`

  User can perform the `replSetHeartbeat` command. Apply this action to the `cluster` resource.

  用户可以执行`replSetHeartbeat`命令。将此操作应用于`cluster`资源。

- `replSetStateChange`

  User can change the state of a replica set through the [`replSetFreeze`](https://docs.mongodb.com/manual/reference/command/replSetFreeze/#dbcmd.replSetFreeze), [`replSetMaintenance`](https://docs.mongodb.com/manual/reference/command/replSetMaintenance/#dbcmd.replSetMaintenance), [`replSetStepDown`](https://docs.mongodb.com/manual/reference/command/replSetStepDown/#dbcmd.replSetStepDown), and [`replSetSyncFrom`](https://docs.mongodb.com/manual/reference/command/replSetSyncFrom/#dbcmd.replSetSyncFrom) commands. Apply this action to the `cluster` resource.

  用户可以通过改变一个副本集的状态通过 [`replSetFreeze`](https://docs.mongodb.com/manual/reference/command/replSetFreeze/#dbcmd.replSetFreeze)，[`replSetMaintenance`](https://docs.mongodb.com/manual/reference/command/replSetMaintenance/#dbcmd.replSetMaintenance)， [`replSetStepDown`](https://docs.mongodb.com/manual/reference/command/replSetStepDown/#dbcmd.replSetStepDown)，和[`replSetSyncFrom`](https://docs.mongodb.com/manual/reference/command/replSetSyncFrom/#dbcmd.replSetSyncFrom) 命令。将此操作应用于`cluster`资源。

- `resync`

  User can perform the `resync` command. Apply this action to the `cluster` resource.
  
  用户可以执行`resync`命令。将此操作应用于`cluster`资源。

## Sharding Actions 

## 分片动作

- `addShard`

  User can perform the [`addShard`](https://docs.mongodb.com/manual/reference/command/addShard/#dbcmd.addShard) command. Apply this action to the `cluster` resource.

  用户可以执行[`addShard`](https://docs.mongodb.com/manual/reference/command/addShard/#dbcmd.addShard)命令。将此操作应用于`cluster`资源。

- `clearJumboFlag`

  Available starting in 4.2.3 and 4.0.15

  Required to clear a chunk’s jumbo flag using the [`clearJumboFlag`](https://docs.mongodb.com/manual/reference/command/clearJumboFlag/#dbcmd.clearJumboFlag) command. Apply this action to database or collection resources.

  Included in the [`clusterManager`](https://docs.mongodb.com/manual/reference/built-in-roles/#clusterManager) built-in role.

  *从4.2.3和4.0.15开始可用*

  使用[`clearJumboFlag`](https://docs.mongodb.com/manual/reference/command/clearJumboFlag/#dbcmd.clearJumboFlag)命令清除块的巨型标志所必需 。将此操作应用于数据库或集合资源。

  包含在[`clusterManager`](https://docs.mongodb.com/manual/reference/built-in-roles/#clusterManager)内置角色中。

- `enableSharding`

  APPLICABLE RESOURCES

  The action can apply to either:

  [Database](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db) or [collection](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db-collection) resource to enable sharding for a database or shard a collection.

  [Cluster](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-collection) resource to perform various shard zone operations (Starting in version 4.2.2, 4.0.14, 3.6.16).

  Resources Description

  [Database](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db) or [Collection](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db-collection)

  Grants users privileges to perform the following operations:Enable sharding on a database using the [`enableSharding`](https://docs.mongodb.com/manual/reference/command/enableSharding/#dbcmd.enableSharding) command, andShard a collection using the [`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection) command.[Cluster](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-collection)*Starting in version 4.2.2, 4.0.14, 3.6.16*Grants users privileges to perform the following shard zone operations:[`addShardToZone`](https://docs.mongodb.com/manual/reference/command/addShardToZone/#dbcmd.addShardToZone)[`updateZoneKeyRange`](https://docs.mongodb.com/manual/reference/command/updateZoneKeyRange/#dbcmd.updateZoneKeyRange)[`removeShardFromZone`](https://docs.mongodb.com/manual/reference/command/removeShardFromZone/#dbcmd.removeShardFromZone)You can also perform these shard zone operations if you have [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)/[`update`](https://docs.mongodb.com/manual/reference/privilege-actions/#update) actions on the appropriate collections in the `config` database. Refer to the specific operations for details.

- | -[数据库](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db) 或者 [集合](https://docs.mongodb.com/manual/reference/resource-document/#resource-specific-db-collection) | 授予用户执行以下操作的权限：使用以下[`enableSharding`](https://docs.mongodb.com/manual/reference/command/enableSharding/#dbcmd.enableSharding)命令在数据库上启用分片 ，然后使用[`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection) 命令对集合进行分片。 |
  | ------------------------------------------------------------ | :----------------------------------------------------------- |
  | *从版本4.2.2、4.0.14、3.6.16开始*                            | 授予用户执行以下分区域操作的权限：[`addShardToZone`](https://docs.mongodb.com/manual/reference/command/addShardToZone/#dbcmd.addShardToZone)、[`updateZoneKeyRange`](https://docs.mongodb.com/manual/reference/command/updateZoneKeyRange/#dbcmd.updateZoneKeyRange)、[`removeShardFromZone`](https://docs.mongodb.com/manual/reference/command/removeShardFromZone/#dbcmd.removeShardFromZone)。如果对数据库中的相应集合执行[`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)/ [`update`](https://docs.mongodb.com/manual/reference/privilege-actions/#update)操作，则还可以执行这些分片区操作`config`。有关详细信息，请参见具体操作。 |

- 

- `flushRouterConfig`

  User can perform the [`flushRouterConfig`](https://docs.mongodb.com/manual/reference/command/flushRouterConfig/#dbcmd.flushRouterConfig) command. Apply this action to the `cluster` resource.

  用户可以执行[`flushRouterConfig`](https://docs.mongodb.com/manual/reference/command/flushRouterConfig/#dbcmd.flushRouterConfig)命令。将此操作应用于`cluster`资源。

- `getShardMap`

  User can perform the [`getShardMap`](https://docs.mongodb.com/manual/reference/command/getShardMap/#dbcmd.getShardMap) command. Apply this action to the `cluster` resource.

  用户可以执行[`getShardMap`](https://docs.mongodb.com/manual/reference/command/getShardMap/#dbcmd.getShardMap)命令。将此操作应用于`cluster`资源。

- `getShardVersion`

  User can perform the [`getShardVersion`](https://docs.mongodb.com/manual/reference/command/getShardVersion/#dbcmd.getShardVersion) command. Apply this action to database resources.

  用户可以执行[`getShardVersion`](https://docs.mongodb.com/manual/reference/command/getShardVersion/#dbcmd.getShardVersion)命令。将此操作应用于数据库资源。

- `listShards`

  User can perform the [`listShards`](https://docs.mongodb.com/manual/reference/command/listShards/#dbcmd.listShards) command. Apply this action to the `cluster` resource.

  用户可以执行[`listShards`](https://docs.mongodb.com/manual/reference/command/listShards/#dbcmd.listShards)命令。将此操作应用于`cluster`资源。

- `moveChunk`

  User can perform the [`moveChunk`](https://docs.mongodb.com/manual/reference/command/moveChunk/#dbcmd.moveChunk) command. In addition, user can perform the [`movePrimary`](https://docs.mongodb.com/manual/reference/command/movePrimary/#dbcmd.movePrimary) command provided that the privilege is applied to an appropriate database resource. Apply this action to database or collection resources.

  用户可以执行[moveChunk](https://docs.mongodb.com/manual/reference/command/moveChunk/#dbcmd.moveChunk)命令。 此外，如果将特权应用于适当的数据库资源，则用户可以执行 [`movePrimary`](https://docs.mongodb.com/manual/reference/command/movePrimary/#dbcmd.movePrimary) 命令。 将此操作应用于数据库或集合资源。

- `removeShard`

  User can perform the [`removeShard`](https://docs.mongodb.com/manual/reference/command/removeShard/#dbcmd.removeShard) command. Apply this action to the `cluster` resource.

  用户可以执行[`removeShard`](https://docs.mongodb.com/manual/reference/command/removeShard/#dbcmd.removeShard)命令。将此操作应用于`cluster`资源。

- `shardingState`

  User can perform the [`shardingState`](https://docs.mongodb.com/manual/reference/command/shardingState/#dbcmd.shardingState) command. Apply this action to the `cluster` resource.

  用户可以执行[`shardingState`](https://docs.mongodb.com/manual/reference/command/shardingState/#dbcmd.shardingState)命令。将此操作应用于`cluster`资源。

- `splitChunk`

  User can perform the [`splitChunk`](https://docs.mongodb.com/manual/reference/command/splitChunk/#dbcmd.splitChunk) command and the [`mergeChunks`](https://docs.mongodb.com/manual/reference/command/mergeChunks/#dbcmd.mergeChunks) command. Apply this action to database or collection resources.

  用户可以执行[`splitChunk`](https://docs.mongodb.com/manual/reference/command/splitChunk/#dbcmd.splitChunk)命令和 [`mergeChunks`](https://docs.mongodb.com/manual/reference/command/mergeChunks/#dbcmd.mergeChunks)命令。将此操作应用于数据库或集合资源。

- `splitVector`

  User can perform the [`splitVector`](https://docs.mongodb.com/manual/reference/command/splitVector/#dbcmd.splitVector) command. Apply this action to database or collection resources.
  
  用户可以执行[`splitVector`](https://docs.mongodb.com/manual/reference/command/splitVector/#dbcmd.splitVector)命令。将此操作应用于数据库或集合资源。

## Server Administration Actions 

## 服务器管理操作

- `applicationMessage`

  User can perform the [`logApplicationMessage`](https://docs.mongodb.com/manual/reference/command/logApplicationMessage/#dbcmd.logApplicationMessage) command. Apply this action to the `cluster` resource.

  用户可以执行[`logApplicationMessage`](https://docs.mongodb.com/manual/reference/command/logApplicationMessage/#dbcmd.logApplicationMessage)命令。将此操作应用于`cluster`资源。

- `closeAllDatabases`

  User can perform the `closeAllDatabases` command. Apply this action to the `cluster` resource.

  用户可以执行`closeAllDatabases`命令。将此操作应用于`cluster`资源。

- `collMod`

  User can perform the [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) command. Apply this action to database or collection resources.

  用户可以执行[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod)命令。将此操作应用于数据库或集合资源。

- `compact`

  User can perform the [`compact`](https://docs.mongodb.com/manual/reference/command/compact/#dbcmd.compact) command. Apply this action to database or collection resources.

  用户可以执行[`compact`](https://docs.mongodb.com/manual/reference/command/compact/#dbcmd.compact)命令。将此操作应用于数据库或集合资源。

- `connPoolSync`

  User can perform the [`connPoolSync`](https://docs.mongodb.com/manual/reference/command/connPoolSync/#dbcmd.connPoolSync) command. Apply this action to the `cluster` resource.

  用户可以执行[`connPoolSync`](https://docs.mongodb.com/manual/reference/command/connPoolSync/#dbcmd.connPoolSync)命令。将此操作应用于`cluster`资源。

- `convertToCapped`

  User can perform the [`convertToCapped`](https://docs.mongodb.com/manual/reference/command/convertToCapped/#dbcmd.convertToCapped) command. Apply this action to database or collection resources.

  用户可以执行[`convertToCapped`](https://docs.mongodb.com/manual/reference/command/convertToCapped/#dbcmd.convertToCapped)命令。将此操作应用于数据库或集合资源。

- `dropConnections`

  User can perform the [`dropConnections`](https://docs.mongodb.com/manual/reference/command/dropConnections/#dbcmd.dropConnections) command. Apply this action to the `cluster` resource.

  用户可以执行[`dropConnections`](https://docs.mongodb.com/manual/reference/command/dropConnections/#dbcmd.dropConnections)命令。将此操作应用于`cluster`资源。

- `dropDatabase`

  User can perform the [`dropDatabase`](https://docs.mongodb.com/manual/reference/command/dropDatabase/#dbcmd.dropDatabase) command. Apply this action to database resources.

  用户可以执行[`dropDatabase`](https://docs.mongodb.com/manual/reference/command/dropDatabase/#dbcmd.dropDatabase)命令。将此操作应用于数据库资源。

- `dropIndex`

  User can perform the [`dropIndexes`](https://docs.mongodb.com/manual/reference/command/dropIndexes/#dbcmd.dropIndexes) command. Apply this action to database or collection resources.

  用户可以执行[`dropIndexes`](https://docs.mongodb.com/manual/reference/command/dropIndexes/#dbcmd.dropIndexes)命令。将此操作应用于数据库或集合资源。

- `forceUUID`

  *New in version 3.6.*

  User can create a collection with a user-defined [collection UUID](https://docs.mongodb.com/manual/core/databases-and-collections/#collections-uuids) using the [`applyOps`](https://docs.mongodb.com/manual/reference/command/applyOps/#dbcmd.applyOps) command.

  Apply this action to the `cluster` resource.

  *3.6版的新功能。*

  用户可以使用命令使用 用户定义的[集合UUID](https://docs.mongodb.com/manual/core/databases-and-collections/#collections-uuids)创建集合 [`applyOps`](https://docs.mongodb.com/manual/reference/command/applyOps/#dbcmd.applyOps)。

  将此操作应用于`cluster`资源。

- `fsync`

  User can perform the [`fsync`](https://docs.mongodb.com/manual/reference/command/fsync/#dbcmd.fsync) command. Apply this action to the `cluster` resource.

  用户可以执行[`fsync`](https://docs.mongodb.com/manual/reference/command/fsync/#dbcmd.fsync)命令。将此操作应用于`cluster`资源。

- `getParameter`

  User can perform the [`getParameter`](https://docs.mongodb.com/manual/reference/command/getParameter/#dbcmd.getParameter) command. Apply this action to the `cluster` resource.

- `hostInfo`

  Provides information about the server the MongoDB instance runs on. Apply this action to the `cluster` resource.

  提供有关运行MongoDB实例的服务器的信息。将此操作应用于`cluster`资源。

- `logRotate`

  User can perform the [`logRotate`](https://docs.mongodb.com/manual/reference/command/logRotate/#dbcmd.logRotate) command. Apply this action to the `cluster` resource.

  用户可以执行[`logRotate`](https://docs.mongodb.com/manual/reference/command/logRotate/#dbcmd.logRotate)命令。将此操作应用于`cluster`资源。

- `reIndex`

  User can perform the [`reIndex`](https://docs.mongodb.com/manual/reference/command/reIndex/#dbcmd.reIndex) command. Apply this action to database or collection resources.

  用户可以执行[`reIndex`](https://docs.mongodb.com/manual/reference/command/reIndex/#dbcmd.reIndex)命令。将此操作应用于数据库或集合资源。

- `renameCollectionSameDB`

  Allows the user to rename collections on the current database using the [`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection) command. Apply this action to database resources.

  Additionally, the user must either *have* [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) on the source collection or *not have* [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) on the destination collection.

  If a collection with the new name already exists, the user must also have the [`dropCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropCollection) action on the destination collection.

  允许用户使用[`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection)命令在当前数据库上重命名集合 。将此操作应用于数据库资源。

  此外，用户必须*拥有* [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)源集合或者*没有* [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)目的地集合。

  如果已经存在使用新名称的集合，则用户还必须[`dropCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropCollection)对目标集合执行操作。

- `setParameter`

  User can perform the [`setParameter`](https://docs.mongodb.com/manual/reference/command/setParameter/#dbcmd.setParameter) command. Apply this action to the `cluster` resource.

  用户可以执行[`setParameter`](https://docs.mongodb.com/manual/reference/command/setParameter/#dbcmd.setParameter)命令。将此操作应用于`cluster`资源。

- `shutdown`

  User can perform the [`shutdown`](https://docs.mongodb.com/manual/reference/command/shutdown/#dbcmd.shutdown) command. Apply this action to the `cluster` resource.

  用户可以执行[`shutdown`](https://docs.mongodb.com/manual/reference/command/shutdown/#dbcmd.shutdown)命令。将此操作应用于`cluster`资源。

- `touch`

  User can perform the `touch` command. Apply this action to the `cluster` resource.
  
  用户可以执行`touch`命令。将此操作应用于`cluster`资源。

## Session Actions

## 会话的操作

- `impersonate`

  *New in version 3.6.*

  User can perform the [`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern) command with `users` and `roles` pattern. Apply this action to the `cluster` resource.

  To run [`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern) command, users must also have [`killAnySession`](https://docs.mongodb.com/manual/reference/privilege-actions/#killAnySession) privileges on the cluster resource.

  *3.6版的新功能。*

  用户可以[`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern)使用`users`和`roles`模式执行命令。将此操作应用于 `cluster`资源。

  要运行[`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern)命令，用户还必须[`killAnySession`](https://docs.mongodb.com/manual/reference/privilege-actions/#killAnySession)对集群资源具有特权。

- `listSessions`

  *New in version 3.6.*

  User can perform the [`$listSessions`](https://docs.mongodb.com/manual/reference/operator/aggregation/listSessions/#pipe._S_listSessions) operation or [`$listLocalSessions`](https://docs.mongodb.com/manual/reference/operator/aggregation/listLocalSessions/#pipe._S_listLocalSessions) operation for all users or specified user(s). Apply this action to the `cluster` resource.

  *3.6版的新功能。*

  用户可以为所有用户或指定用户执行[`$listSessions`](https://docs.mongodb.com/manual/reference/operator/aggregation/listSessions/#pipe._S_listSessions)一项或 [`$listLocalSessions`](https://docs.mongodb.com/manual/reference/operator/aggregation/listLocalSessions/#pipe._S_listLocalSessions)多项操作。将此操作应用于`cluster`资源。

- `killAnySession`

  *New in version 3.6.*
  
  User can perform the [`killAllSessions`](https://docs.mongodb.com/manual/reference/command/killAllSessions/#dbcmd.killAllSessions) and the [`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern) command. Apply this action to the `cluster` resource.SEE ALSO[`impersonate`](https://docs.mongodb.com/manual/reference/privilege-actions/#impersonate)
  
  用户可以执行[`killAllSessions`](https://docs.mongodb.com/manual/reference/command/killAllSessions/#dbcmd.killAllSessions)和 [`killAllSessionsByPattern`](https://docs.mongodb.com/manual/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern)命令。将此操作应用于`cluster`资源。**也可以看看**[`impersonate`](https://docs.mongodb.com/manual/reference/privilege-actions/#impersonate)

## Free Monitoring Actions 

## 免费监控动作

- `checkFreeMonitoringStatus`

  User with this action on the `cluster` resource can check the status of [Free Monitoring](https://docs.mongodb.com/manual/administration/free-monitoring/).

  *New in version 4.0.*

  对`cluster`资源执行此操作的用户可以检查“ [免费监视”](https://docs.mongodb.com/manual/administration/free-monitoring/)的状态。

  *版本4.0中的新功能。*

- `setFreeMonitoring`

  User with this action on the `cluster` resource can enable or disable [Free Monitoring](https://docs.mongodb.com/manual/administration/free-monitoring/).
  
  *New in version 4.0.*
  
  对`cluster`资源执行此操作的用户可以启用或禁用“ [免费监视”](https://docs.mongodb.com/manual/administration/free-monitoring/)。
  
  *版本4.0中的新功能。*

## Diagnostic Actions 

## 诊断动作

- `collStats`

  User can perform the [`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats) command. Apply this action to database or collection resources.

  用户可以执行[`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats)命令。将此操作应用于数据库或集合资源。

- `connPoolStats`

  User can perform the [`connPoolStats`](https://docs.mongodb.com/manual/reference/command/connPoolStats/#dbcmd.connPoolStats) and [`shardConnPoolStats`](https://docs.mongodb.com/manual/reference/command/shardConnPoolStats/#dbcmd.shardConnPoolStats) commands. Apply this action to the `cluster` resource.

  用户可以执行[`connPoolStats`](https://docs.mongodb.com/manual/reference/command/connPoolStats/#dbcmd.connPoolStats)和[`shardConnPoolStats`](https://docs.mongodb.com/manual/reference/command/shardConnPoolStats/#dbcmd.shardConnPoolStats) 命令。将此操作应用于`cluster`资源。

- `cursorInfo`

  User can perform the [`cursorInfo`](https://docs.mongodb.com/manual/reference/command/cursorInfo/#dbcmd.cursorInfo) command. Apply this action to the `cluster` resource.

  用户可以执行[`cursorInfo`](https://docs.mongodb.com/manual/reference/command/cursorInfo/#dbcmd.cursorInfo)命令。将此操作应用于`cluster`资源。

- `dbHash`

  User can perform the [`dbHash`](https://docs.mongodb.com/manual/reference/command/dbHash/#dbcmd.dbHash) command. Apply this action to database or collection resources.

  用户可以执行[`dbHash`](https://docs.mongodb.com/manual/reference/command/dbHash/#dbcmd.dbHash)命令。将此操作应用于数据库或集合资源。

- `dbStats`

  User can perform the [`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats) command. Apply this action to database resources.

  用户可以执行[`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats)命令。将此操作应用于数据库资源。

- `getCmdLineOpts`

  User can perform the [`getCmdLineOpts`](https://docs.mongodb.com/manual/reference/command/getCmdLineOpts/#dbcmd.getCmdLineOpts) command. Apply this action to the `cluster` resource.

  用户可以执行[`getCmdLineOpts`](https://docs.mongodb.com/manual/reference/command/getCmdLineOpts/#dbcmd.getCmdLineOpts)命令。将此操作应用于`cluster`资源。

- `getLog`

  User can perform the [`getLog`](https://docs.mongodb.com/manual/reference/command/getLog/#dbcmd.getLog) command. Apply this action to the `cluster` resource.

  用户可以执行[`getLog`](https://docs.mongodb.com/manual/reference/command/getLog/#dbcmd.getLog)命令。将此操作应用于`cluster`资源。

- `indexStats`

  User can perform the `indexStats` command. Apply this action to database or collection resources.

  *Changed in version 3.0:* MongoDB 3.0 removes the `indexStats` command.

  用户可以执行`indexStats`命令。将此操作应用于数据库或集合资源。

  *在版本3.0*中进行了*更改：* MongoDB 3.0删除了该`indexStats`命令。

- `listDatabases`

  User can perform the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command. Apply this action to the `cluster` resource.

  For MongoDB 4.0.6+:

  If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has privileges (including databases for which the user has privileges on specific collections) if the command is run with `authorizedDatabases` option unspecified or set to `true`.

  对于MongoDB 4.0.6+:

  如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作， 如果运行命令[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases)时`authorizedDatabases`参数未指定或设置为true，则用户可以通过运行[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases)命令以返回该用户具有特权的数据库的列表（包括该用户对其特定集合具有特权的数据库）。

  For MongoDB 4.0.5:

  If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has the [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) action privilege if the command is run with `authorizedDatabases` option unspecified or set to `true`.

  如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作，在未指定`authorizedDatabases参数或设置为true的情况下运行[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) 命令时，可以返回用户对其具有[`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)操作特权 的数据库的列表 。

  For MongoDB 4.0.0-4.0.4:

  If the user does not have the [`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) privilege action, users can run the [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) command to return a list of databases for which the user has the [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) action privilege.

  **对于MongoDB 4.0.0-4.0.4：**

  如果用户没有[`listDatabases`](https://docs.mongodb.com/manual/reference/privilege-actions/#listDatabases) 特权操作，则用户可以运行[`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) 命令以返回该用户对其具有[`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find)操作特权的数据库列表 。

- `listCollections`

  User can perform the [`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections) command. Apply this action to database resources.

  用户可以执行[`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections)命令。将此操作应用于数据库资源。

  NOTE

  Starting in version 4.0, user without the required privilege can run the [`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections) command with **both** `authorizedCollections` and `nameOnly` options set to `true`. In this case, the command returns just the name and type of the collection(s) to which the user has privileges.

  从4.0版开始，没有所需特权的用户可以在 `authorizedCollections`和 nameOnly 选项都设置为 true 的情况下运行[`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections)命令。在这种情况下，该命令仅返回用户具有特权的集合的名称和类型。

- `listIndexes`

  User can perform the [`listIndexes`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes) command. Apply this action to database or collection resources.

  用户可以执行[`listIndexes`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)命令。将此操作应用于数据库或集合资源。

- `netstat`

  User can perform the [`netstat`](https://docs.mongodb.com/manual/reference/command/netstat/#dbcmd.netstat) command. Apply this action to the `cluster` resource.

  用户可以执行[`netstat`](https://docs.mongodb.com/manual/reference/command/netstat/#dbcmd.netstat)命令。将此操作应用于`cluster`资源。

- `serverStatus`

  User can perform the [`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus) command. Apply this action to the `cluster` resource.

  用户可以执行[`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus)命令。将此操作应用于`cluster`资源。

- `validate`

  User can perform the [`validate`](https://docs.mongodb.com/manual/reference/command/validate/#dbcmd.validate) command. Apply this action to database or collection resources.

  用户可以执行[`validate`](https://docs.mongodb.com/manual/reference/command/validate/#dbcmd.validate)命令。将此操作应用于数据库或集合资源。

- `top`

  User can perform the [`top`](https://docs.mongodb.com/manual/reference/command/top/#dbcmd.top) command. Apply this action to the `cluster` resource.
  
  用户可以执行[`top`](https://docs.mongodb.com/manual/reference/command/top/#dbcmd.top)命令。将此操作应用于 `cluster`资源。

## Internal Actions

## 内部操作

- `anyAction`

  Allows any action on a resource. **Do not** assign this action unless it is absolutely necessary.

  允许对资源执行任何操作。除非绝对必要，否则**不要**分配此操作。

- `internal`

  Allows internal actions. **Do not** assign this action unless it is absolutely necessary.
  
  允许内部动作。除非绝对必要，否则**不要**分配此操作。
  
  
