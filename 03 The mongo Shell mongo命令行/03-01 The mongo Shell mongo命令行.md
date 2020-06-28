# The mongo Shell[](https://docs.mongodb.com/manual/mongo/ "Permalink to this headline")
# mongo 命令行


-   [Start the mongo Shell and Connect to MongoDB](https://docs.mongodb.com/manual/mongo/#start-the-mongo-shell-and-connect-to-mongodb)开始mongo命令行并连接MongoDB
-   [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)用mongo命令行工作
-   [Tab Completion and Other Keyboard Shortcuts](https://docs.mongodb.com/manual/mongo/#tab-completion-and-other-keyboard-shortcuts)选项卡完成和其他键盘快捷键
-   [.mongorc.js File](https://docs.mongodb.com/manual/mongo/#mongorc-js-file) .mongorc.js文件
-   [Exit the Shell](https://docs.mongodb.com/manual/mongo/#exit-the-shell)退出命令行

The mongo shell is an interactive JavaScript interface to MongoDB. You can use the mongo shell to query and update data as well as perform administrative operations.
mongo shell通过JavaScript接口和MongoDB进行交互。您可以使用mongo shell去查询和更新数据以及执行管理操作。

The mongo shell is included as part of the MongoDB Server installation. MongoDB also provides the mongo shell as a standalone package. To download the standalone mongo shell package:
mongo shell在MongoDB服务安装时就已集成。MongoDB还提供mongo shell作为一个独立的包。下载独立mongo shell包：

1. Open the [Download Center](https://www.mongodb.com/download-center/community?jmp=docs). For the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) Enterprise Shell, select the MongoDB Enterprise Server tab.
打开下载中心。对于企业mongo Shell,选择MongoDB Enterprise Server选项卡

2. Select your preferred Version and OS from the dropdowns.从下拉框选择适合您的版本和操作系统

3. Select the Package to download according to your platform:选择适合您的操作系统进行下载

 Platform平台  Download Package 下载包

 - Windows | Select the ZIP package to download an archive which includes the mongo shell.windows选择zip包进行下载
 - macOS | Select the TGZ package to download an archive which includes the mongo shell.macOS选择TGZ包进行下载
 - Linux |	Select the shell package to download the mongo shell.linux选择shell包进行下载

Once you have installed and have started MongoDB, connect the mongo shell to your running MongoDB instance.一旦安装并启动MongoDB，就可以通过mongo shell去运行MongoDB实例。

NOTE
注意
Starting in MongoDB 4.2 (and 4.0.13), the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell displays a warning message when connected to non-genuine MongoDB instances as these instances may behave differently from the official MongoDB instances; e.g. missing or incomplete features, different feature behaviors, etc.
从MongoDB 4.0.13和4.2版本开始，当连接和官方MongoDB有区别的非官方MongoDB实例时，mongo shell 将提示警告信息；例如缺少或不完整功能，或不一样的功能。

## Start the mongo Shell and Connect to MongoDB 开始用mongo shell连接MongoDB

### Prerequisites 前提

Ensure that MongoDB is running before attempting to start the mongo shell. 尝试启动mongo shell时，确保MongoDB已经运行。

Open a terminal window (or a command prompt for Windows) and go to your <mongodb installation dir>/bin directory:打开终端窗口(或Windows命令提示符)并进入<安装mongodb 目录 > / bin目录:

```
cd <mongodb installation dir>/bin
```

TIP小提示

Adding your <mongodb installation dir>/bin to the PATH environment variable allows you to type mongo instead of having to go to the <mongodb installation dir>/bin directory or specify the full path to the binary.
添加MongoDB安装目录下的bin目录到系统环境变量中，这样你可以在任何地方执行mongo命令行，而不需要到MongoDB安装目录去执行。

### Local MongoDB Instance on Default Port 用默认端口连接本地MongoDB实例

You can run mongo shell without any command-line options to connect to a MongoDB instance running on your localhost with default port 27017:
默认不添加任何参数，执行如下mongo命令，以27012端口连接MongoDB实例：

```
mongo
```

### Local MongoDB Instance on a Non-default Port 用非默认端口连接本地实例

To explicitly specify the port, include the --port command-line option. For example, to connect to a MongoDB instance running on localhost with a non-default port 28015:
以--port 命令行方式指定端口启动MongoDB实例。例如用一个非默认的28015端口启动MongoDB实例：

```
mongo --port 28015
```
### MongoDB Instance on a Remote Host 连接远程MongoDB实例

To explicitly specify the hostname and/or port, 指定主机名和/或端口,

- You can specify a connection string. For example, to connect to a MongoDB instance running on a remote host machine:您可以指定一个连接字符串。例如,连接MongoDB实例运行在远程主机上

```
mongo "mongodb://mongodb0.example.com:28015"
```

- You can use the command-line option --host <host>:<port>. For example, to connect to a MongoDB instance running on a remote host machine:您可以使用命令行选项-主机<主机>:<口>。例如,连接远程主机的MongoDB实例:

```
mongo --host mongodb0.example.com:28015
```

- You can use the --host <host> and --port <port> command-line options. For example, to connect to a MongoDB instance running on a remote host machine:您可以使用——主机> <主机和端口<口>命令行选项进行连接。例如,连接远程主机的MongoDB实例:

```
mongo --host mongodb0.example.com --port 28015
```

### MongoDB Instance with Authentication 启用身份验证的MongoDB实例

To connect to a MongoDB instance requires authentication:   连接到需要身份验证的MongoDB实例:

- You can specify the username, authentication database, and optionally the password in the connection string. For example, to connect and authenticate to a remote MongoDB instance as user alice:您可以在连接信息中指定用户名、身份验证信息以及密码。例如,连接远程MongoDB实例以alice作为用户进行身份验证

NOTE 注意

If you do not specify the password in the connection string, the shell will prompt for the password.如果您没有在连接字符串中指定密码,shell将提示输入密码

```
mongo "mongodb://alice@mongodb0.examples.com:28015/?authSource=admin"
```

- You can use the --username <user> and --password, --authenticationDatabase <db> command-line options. For example, to connect and authenticate to a remote MongoDB instance as user alice:您可以使用——username和--password，——authenticationDatabase < db >命令行选项进行连接。例如,连接远程MongoDB实例以alice作为用户进行身份验证

NOTE 注意

If you specify --password without the user’s password, the shell will prompt for the password.如果您输入--password后面没有用户密码,shell将提示输入密码

```
mongo --username alice --password --authenticationDatabase admin --host mongodb0.examples.com --port 28015
```

### Connect to a MongoDB Replica Set 连接到MongoDB副本集

To connect to a replica set: 连接到副本集

- You can specify the replica set name and members in the connection string.您可以在连接字符串中指定副本集名称和成员。

```
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA"
```

- If using the [DNS Seedlist Connection Format](https://docs.mongodb.com/manual/reference/connection-string/#connections-dns-seedlist), you can specify the connection string:如果使用DNS Seedlist连接格式,您可以在连接字符串中指定:  

```
mongo "mongodb+srv://server.example.com/"
```

Note 注意

Use of the +srv connection string modifier automatically sets the ssl option to true for the connection.使用+ srv字符串修正符进行连接，会自动设置ssl选项为true。

- You can specify the replica set name and members from the --host <replica set name>/<host1>:<port1>,<host2>:<port2>,... command-line option. For example, to connect to replica set named replA:您可以指定副本集名称和成员以——host<副本集名称> / < host1 >: <port1 >、< host2 >: <port2 >,……命令行方式。例如,连接到名称为replA的副本集:

```
mongo --host replA/mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017
```

### TLS/SSL Connection  TLS/SSL连接

For TLS/SSL connections, 为了TLS/SSL连接

- You can specify the ssl=true option in the connection string.您可以在连接字符串中指定ssl = true。

```
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA&ssl=true"
```

- If using the [DNS Seedlist Connection Format](https://docs.mongodb.com/manual/reference/connection-string/#connections-dns-seedlist), you can include the +srv connection string modifier: 如果使用DNS Seedlist连接格式,在连接字符串中要包含+ srv串修饰符:

```
mongo "mongodb+srv://server.example.com/"
```
Note 注意

Use of the +srv connection string modifier automatically sets the ssl option to true for the connection.使用+ srv字符串修正符进行连接，会自动设置ssl选项为true。

- You can specify --ssl command-line option. For example, to connect to replica set named replA:您可以指定- ssl命令行选项。例如,连接到名称为replA的副本集:

``` 
mongo --ssl --host replA/mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017
```

SEE ALSO: 最后

For more information on the options used in the connection examples as well as other options, see [mongo reference](https://docs.mongodb.com/manual/reference/program/mongo/) and [examples of starting up mongo](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-usage-examples).想获取更多关于连接示例中使用的参数选项以及其他参数选项,可以到mongo参考和启动MongoDB示例中进行查询。

## Working with the mongo Shell 使用mongo shell

To display the database you are using, type db:查看您所使用的数据库,数据库类型:

```
db
```

The operation should return test, which is the default database.  该操作会返回测试库,这是默认的数据库。

To switch databases, issue the use <db> helper, as in the following example: 如下示例，切换数据库使用 use <db>命令

```
use <database>
```

See also [db.getSiblingDB()](https://docs.mongodb.com/manual/reference/method/db.getSiblingDB/#db.getSiblingDB) method to access a different database from the current database without switching your current database context (i.e. db).在不切换数据库上下文环境的情况下，可以使用 db.getSiblingDB()访问其他数据库

To list the databases available to the user, use the helper show dbs. [[1]](https://docs.mongodb.com/manual/mongo/#access)使用show  dbs列出该用户可用的数据库。

You can switch to non-existing databases. When you first store data in the database, such as by creating a collection, MongoDB creates the database. For example, the following creates both the database myNewDatabase and the [collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection) myCollection during the [insertOne()](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) operation:你可以切换到不存在的数据库。当你第一次将数据存储在不存在的数据库中,如创建一个集合,则数据库和集合会一并创建。例如,下面在执行insertOne时会创建数据库myNewDatabase和集合myCollection:

```
use myNewDatabase
db.myCollection.insertOne( { x: 1 } );
```

The [db.myCollection.insertOne()](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) is one of the [methods available in the mongo shell](https://docs.mongodb.com/manual/reference/method/ ).db.myCollection.insertOne()是mongo shell可用的方法之一。

- db refers to the current database.db是指当前数据库。

- myCollection is the name of the collection.myCollection是集合名词。

If the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell does not accept the name of a collection, you can use the alternative [db.getCollection()](https://docs.mongodb.com/manual/reference/method/db.getCollection/#db.getCollection) syntax. For instance, if a collection name contains a space or hyphen, starts with a number, or conflicts with a built-in function:如果mongo shell不识别一个集合名称,您可以使用替代语法db.getCollection()。例如,如果一个集合名称包含一个空格或连字符,或以一个数字开头,或与一个内置函数冲突:

```
db.getCollection("3 test").find()
db.getCollection("3-test").find()
db.getCollection("stats").find()
```

The [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell prompt has a limit of 4095 codepoints for each line. If you enter a line with more than 4095 codepoints, the shell will truncate it.mongo shell限制每行4095个字符，如果超过4095个字符，mongo shell会进行截断。

For more documentation of basic MongoDB operations in the mongo shell, see: mongo shell中更多关于MongoDB的基本操作，如下：

- [Getting Started Guide](https://docs.mongodb.com/manual/tutorial/getting-started/)

- [Insert Documents](https://docs.mongodb.com/manual/tutorial/insert-documents/)

- [Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)

- [Update Documents](https://docs.mongodb.com/manual/tutorial/update-documents/)

- [Delete Documents](https://docs.mongodb.com/manual/tutorial/remove-documents/)

- [mongo Shell Methods](https://docs.mongodb.com/manual/reference/method/)

[[1]](https://docs.mongodb.com/manual/mongo/#id1)	If the deployment runs with access control, the operation returns different values based on user privileges. See [listDatabases Behavior](https://docs.mongodb.com/manual/reference/command/listDatabases/#listdatabases-behavior) for details. 如果以访问控制的方式部署运行,根据用户权限的不同会返回不同的结果。有关详细信息,请参阅listDatabases细节

### Format Printed Results 格式化打印结果

The [db.collection.find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) method returns a [cursor](https://docs.mongodb.com/manual/reference/glossary/#term-cursor) to the results; however, in the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, if the returned cursor is not assigned to a variable using the var keyword, then the cursor is automatically iterated up to 20 times to print up to the first 20 documents that match the query. The [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell will prompt Type it to iterate another 20 times. db.collection.find()方法返回一个游标结果;然而,在mongo shell中,如果返回的游标不使用var关键字分配给一个变量,然后光标会自动打印与查询匹配的前20个文档。接着mongo shell将提示输入游标继续打印后面的20个文档。

To format the printed result, you can add the .pretty() to the operation, as in the following: 您可以添加.pretty()来格式打印结果,如下:

```
db.myCollection.find().pretty()
```

In addition, you can use the following explicit print methods in the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 此外,您可以在mongo shell使用以下更明确的打印方法:

- print() to print without formatting print()打印非格式化的数据

- print(tojson(<obj>)) to print with [JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json) formatting and equivalent to printjson()  print(tojson(<obj>)) 和 printjson() 一样打印json格式数据

- printjson() to print with [JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json) formatting and equivalent to print(tojson(<obj>))  printjson() 和 print(tojson(<obj>))  一样打印json格式数据

For more information and examples on cursor handling in the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, see [Iterate a Cursor in the mongo Shell](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/). See also [Cursor Help](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#mongo-shell-help-cursor) for list of cursor help in the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell. 更多关于游标在mongo shell的操作,可以参考在mongo shell中迭代游标章节。也可在mongo shell中查看游标帮助列表。

### Multi-line Operations in the mongo Shell 在mongo Shell多行操作

If you end a line with an open parenthesis ('('), an open brace ('{'), or an open bracket ('['), then the subsequent lines start with ellipsis ("...") until you enter the corresponding closing parenthesis (')'), the closing brace ('}') or the closing bracket (']'). The [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell waits for the closing parenthesis, closing brace, or the closing bracket before evaluating the code, as in the following example:  如果您以('(')、('{')、('[')结束,那么后续行会以省略号(…)开始,直到您输入相应的(')')、('}')、(']')才会结束。mongo shell直到关闭符号(')')、('}')、(']')，才会执行,像下面的例子:

```
> if ( x > 0 ) {
... count++;
... print (x);
... }
```

You can exit the line continuation mode if you enter two blank lines, as in the following example:输入两个空行您可以退出行延续模式,像下面的例子:

```
> if (x > 0
...
...
>
```

### Tab Completion and Other Keyboard Shortcuts tab补全和其他快捷键

The [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell supports keyboard shortcuts. For example, mongo shell支持快捷键，例如：

- Use the up/down arrow keys to scroll through command history. See [.dbshell](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-dbshell-file) documentation for more information on the .dbshell file. 使用向上/向下箭头键滚动命令历史。可以到 .dbshell文档了解更多信息

- Use <Tab> to autocomplete or to list the completion possibilities, as in the following example which uses <Tab> to complete the method name starting with the letter 'c': 使用<tap>自动完成或者列出可能的列表,如以下示例使用<tap>自动补全以字母“c”开头的方法:

```
db.myCollection.c<Tab>
```

Because there are many collection methods starting with the letter 'c', the <Tab> will list the various methods that start with 'c'. 因为有很多集和方法以字母“c”开头, <tap>将列出各种以“c”开头的方法。

For a full list of the shortcuts, see [Shell Keyboard Shortcuts](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-keyboard-shortcuts) 到Shell Keyboard Shortcuts查看完整快捷键列表

## .mongorc.js File  .mongorc.js文件

When starting, [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) checks the user’s [HOME](https://docs.mongodb.com/manual/reference/program/mongo/#envvar-HOME) directory for a JavaScript file named [.mongorc.js](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-mongorc-file). If found, [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) interprets the content of .mongorc.js before displaying the prompt for the first time. If you use the shell to evaluate a JavaScript file or expression, either by using the [--eval](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-mongo-eval) option on the command line or by specifying [a .js file to mongo](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-shell-file), [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) will read the .mongorc.js file after the JavaScript has finished processing. You can prevent .mongorc.js from being loaded by using the [--norc](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-mongo-norc) option. MongoDB会检查用户的主目录的一个名为.mongorc.js的JavaScript文件，如果存在，会在第一次命令行提示前执行该js文件。如果用mongo shell 执行JavaScript文件或JavaScript表达式，或者通过--eval执行，或者创建一个a.js文件执行，在这些JavaScript执行完，会执行.mongorc.js 文件。您可以使用--norc 防止.mongorc.js被执行。

### Exit the Shell 退出shell

To exit the shell, type quit() or use the <Ctrl-C> shortcut. 用 quit()或者<Ctrl-C>快捷键退出shell

SEE ALSO： 参见

- [Getting Started Guide](https://docs.mongodb.com/manual/tutorial/getting-started/) 开始引导

- [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) Reference Page mongo参考页



原文链接：https://docs.mongodb.com/manual/mongo/

译者：金江

