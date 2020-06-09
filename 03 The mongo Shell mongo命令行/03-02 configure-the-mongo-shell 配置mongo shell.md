# Configure the mongo Shell 配置mongo shell

On this page 本页内容

- Customize the Prompt[](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#customize-the-prompt) 自定义提示
- Use an External Editor in the mongo Shell[](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#use-an-external-editor-in-the-mongo-shell)在mongo shell中使用外部编辑器
- Change the mongo Shell Batch Size[](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#change-the-mongo-shell-batch-size)修改mongo shell 每次返回的结果集大小


## Customize the Prompt 自定义提示

You may modify the content of the prompt by setting the variable prompt in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell. The prompt variable can hold strings as well as JavaScript code. If prompt holds a function that returns a string, mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) can display dynamic information in each prompt. 你可以在mongo shell中通过设置变量prompt来修改提示内容。变量prompt可以保存字符串和JavaScript代码。如果提示一个函数,这个函数返回一个字符串,mongo可以显示动态信息在每一个提示。

You can add the logic for the prompt in the .mongorc.js[](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-mongorc-file) file to set the prompt each time you start up the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell.每次启动mongo shell时，可以通过在.mongorc.js文件添加逻辑来设置提示。

### Customize Prompt to Display Number of Operations  自定义提示显示操作的数量

For example,to create a mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell prompt with the number of operations issued in the current session, define the following variables in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 例如，在当前会话创建一个带命令条数的提示，在mongo shell中按如下设置：

```
cmdCount = 1;
prompt = function() {
             return (cmdCount++) + "> ";
         }
```

The prompt would then resemble the following:提示如下所示：

```
1>
2>
3>
```

### Customize Prompt to Display Database and Hostname 自定义提示线上数据库和主机名

To create a mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell prompt in the form of < database >@< hostname >$, define the following variables:自定义提示按照<数据库>@<主机名>$格式

```
host = db.serverStatus().host;

prompt = function() {
             return db+"@"+host+"$ ";
         }
```

The prompt would then resemble the following: 提示如下所示：

```
test@myHost1$
```

### Customize Prompt to Display Up Time and Document Count 自定义提示显示开机时间和文档数量

To create a mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell prompt that contains the system up time and the number of documents in the current database, define the following prompt variable in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell: 如下，定义一个包含系统开机时间和文档个数的提示：

```
prompt = function() {
           return "Uptime:"+db.serverStatus().uptime+" Documents:"+db.stats().objects+" > ";
         }
```

The prompt would then resemble the following: 提示如下所示：

```
Uptime:5897 Documents:6 >
```

## Use an External Editor in the mongo Shell 在mongo shell中使用外部编辑器

You can use your own editor in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell by setting the EDITOR[](https://docs.mongodb.com/manual/reference/program/mongo/#envvar-EDITOR) environment variable before starting the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell.在启动mongo shell前，你可以设置EDITOR环境变量来使用自己的编辑器。

```
export EDITOR=vim
mongo
```

Once in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, you can edit with the specified editor by typing edit < variable > or edit < function >, as in the following example:在mongo shell中,你可以通过输入 edit< variable > 或者 edit < function >来进行编辑，如下所示：
1. Define a function myFunction: 定义一个方法myFunction
    ```
    function myFunction () { }
    ```
2. Edit the function using your editor: 用自定义的编辑器编辑方法
    ```
    edit myFunction
    ```
    The command should open the vim edit session. When finished with the edits, save and exit vim edit session.该命令会打开vim编辑器。当完成编辑，保存并退出编辑器。

3. In the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, type myFunction to see the function definition: 在mongo shell中，输入myFunction查看方法定义
    ```
    myFunction
    ```

    The result should be the changes from your saved edit:可以看到通过编辑器修改后的结果
    ```
    function myFunction() {
        print("This was edited");
    }
    ```

NOTE 注意

As mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell interprets code edited in an external editor, it may modify code in functions, depending on the JavaScript compiler. For example, mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) may convert 1+1 to 2 or remove comments. The actual changes affect only the appearance of the code and will vary based on the version of JavaScript used but will not affect the semantics of the code. 当mongo shell解释外部编辑器编辑的代码，可能修改方法中的代码，这依赖JavaScript编译器。例如，mongo可能把1+1转化成2或者删掉注释。实际上仅仅改变代码外在形式，这依赖JavaScript版本，并不会改变代码语义。

## Change the mongo Shell Batch Size 修改mongo shell 每次返回的结果集大小

The db.collection.find()[](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) method is the JavaScript method to retrieve documents from a collection[](https://docs.mongodb.com/manual/reference/glossary/#term-collection). The db.collection.find()[](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) method returns a cursor[](https://docs.mongodb.com/manual/reference/glossary/#term-cursor) to the results; however, in the mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell, if the returned cursor is not assigned to a variable using the var keyword, then the cursor is automatically iterated up to 20 times to print up to the first 20 documents that match the query. The mongo[](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell will prompt Type it to iterate another 20 times. db.collection.find()是一个从集合中检索文档的JavaScript方法。db.collection.find()方法返回一个游标结果;然而,在mongo shell,如果返回的游标不使用var关键字分配给一个变量,光标会自动迭代符合查询条件的文档20次直到开始的20个文档。mongo shell将提示输入以迭代另外20次。

You can set the DBQuery.shellBatchSize attribute to change the number of documents from the default value of 20, as in the following example which sets it to 10: 如下你可以将 DBQuery.shellBatchSize属性由默认值20改为10

```
DBQuery.shellBatchSize = 10;
```



原文链接：https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/

译者：金江