# Access the mongo Shell Help 查看mongo shell帮助文档

On this page 本页内容

- Command Line Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#command-line-help) 命令行帮助
- Shell Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#shell-help) shell帮助
- Database Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#database-help) 数据库帮助
- Collection Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#collection-help) 集合帮助
- Cursor Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#cursor-help) 游标帮助
- Wrapper Object Help[](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#wrapper-object-help) 包装对象帮助

In addition to the documentation in the MongoDB Manual[](https://docs.mongodb.com/manual/), the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell provides some additional information in its “online” help system. This document provides an overview of accessing this help information.除了MongoDB文档手册,mongo shell通过在线帮助系统提供了一些额外的信息。本文概述了如何访问这些帮助信息。

## Command Line Help 命令行帮助

To see the list of options and help for starting the mongo shell, use the --help option from the command line: 通过在命令行输入--help来查看mongo shell的帮助文档
```
mongo --help
```

## Shell Help shell帮助

To see the list of help, in the mongo shell, type help: 在mongo shell中输入help查看帮助文档

```
help
```
## Database Help 数据库帮助

In the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 在mongo shell中

* To see the list of databases on the server, use the show dbs command: [1][](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#access) 查看服务器数据库列表使用 show dbs命令

    ```
    show dbs
    ```
    show databases is an alias for show dbs. show databases是show dbs的别称
* To see the list of help for methods you can use on the db object, call the db.help()[](https://docs.mongodb.com/manual/reference/method/db.help/#db.help) method:使用db.help()查看db对象的全部帮助方法

    ```
    db.help()
    ```

* To see the implementation of a method in the shell, type the db.< method name > without the parenthesis (()), as in the following example which will return the implementation of the method db.updateUser(): 在mongo shell中使用不带括号的db.< method name >命令查看一个方法的实现，如下返回方法 db.updateUser()的实现。
    ```
    db.updateUser
    ```

[1][](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#access)	If the deployment runs with access control, the operation returns different values based on user privileges. See listDatabases Behavior[](https://docs.mongodb.com/manual/reference/command/listDatabases/#listdatabases-behavior) for details. 如果以访问控制模式运行，根据用户权限不同返回不同的值。可以到listDatabases Behavior 查看详情。

## Collection Help 集合帮助

In the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 在mongo shell中

* To see the list of collections in the current database, use the show collections command: 使用show collections查看当前数据库全部集合列表。

    ```
    show collections
    ```
    SEE ALSO: 也可以查看
    show collections[](https://docs.mongodb.com/manual/release-notes/4.0-compatibility/#compat-show-collections)

* To see the help for methods available on the collection objects (e.g. db.< collection >), use the db.< collection >.help() method: 使用db.< collection >.help()查看集合可使用的帮助方法

    ```
    db.collection.help()
    ```
    < collection > can be the name of a collection that exists, although you may specify a collection that doesn’t exist. 集合名字可以是已存在的也可以是不存在的。

* To see the collection method implementation, type the db.< collection >.<  method > name without the parenthesis (()), as in the following example which will return the implementation of the save()[](https://docs.mongodb.com/manual/reference/method/db.collection.save/#db.collection.save) method: 在mongo shell中使用不带括号的db.< collection >.< method >命令查看一个集合方法的实现，如下返回 save()方法的实现。

    ```
    db.collection.save
    ```

## Cursor Help 游标帮助

When you perform read operations[](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-queries) with the find()[](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) method in the mongo shell, you can use various cursor methods to modify the find()[](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) behavior and various JavaScript methods to handle the cursor returned from the find()[](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) method. 当你在mongo shell中通过find()方法执行读操作，你可以使用各种游标方法修改find()行为，各种JavaScript方法控制游标返回的结果。

* To list the available modifier and cursor handling methods, use the db.collection.find().help() command:使用 db.collection.find().help()命令列出可用的修饰符和游标处理方法：

    ```
    db.collection.find().help()
    ```
    < collection > can be the name of a collection that exists, although you may specify a collection that doesn’t exist.集合名字可以是已存在的也可以是不存在的。

* To see the implementation of the cursor method, type the db.< collection >.find().< method > name without the parenthesis (()), as in the following example which will return the implementation of the toArray() method: 在mongo shell中使用不带括号的db.< collection >.find().< method >命令查看一个游标方法的实现，如下返回 toArray()方法的实现。

    ```
    db.collection.find().toArray
    ```

Some useful methods for handling cursors are:
处理游标的一些有用方法是：

* hasNext()[](https://docs.mongodb.com/manual/reference/method/cursor.hasNext/#cursor.hasNext) which checks whether the cursor has more documents to return. hasNext()检查游标是否有更多文档返回

* next()[](https://docs.mongodb.com/manual/reference/method/cursor.next/#cursor.next) which returns the next document and advances the cursor position forward by one. next()返回下一个文档，并且游标前进一位。

* forEach(< function >)[](https://docs.mongodb.com/manual/reference/method/cursor.forEach/#cursor.forEach) which iterates the whole cursor and applies the < function > to each document returned by the cursor. The < function > expects a single argument which corresponds to the document from each iteration. forEach(< function >)迭代所有游标并将< function >作用于每个文档。< function >需要一个参数对应于每个迭代的文档。

For examples on iterating a cursor and retrieving the documents from the cursor, see cursor handling[](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/). See also Cursor[](https://docs.mongodb.com/manual/reference/method/#js-query-cursor-methods) for all available cursor methods. 可以通过cursor handling查看更多迭代游标和检索文档的示例。通过Cursor查看更多可用的游标方法。

## Wrapper Object Help 包装对象帮助

To get a list of the wrapper classes available in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, such as BinData(), type help misc in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 在mongo shell中输入BinData()或者help misc，可以得到一个可用的包装器类列表。

```
help misc
```
SEE ALSO:参见

mongo Shell Methods[](https://docs.mongodb.com/manual/reference/method/)mongo shell 方法集



原文链接：https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/

译者：金江

