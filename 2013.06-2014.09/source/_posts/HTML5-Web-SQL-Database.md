title: HTML5之Web SQL Database
date: 2014-02-28 12:14:57
categories: HTML5
tags:
---
对于Web应用的存储，使用最多的莫过于Cookie了。Cookie用于弥补HTTP协议的无状态性，服务器可以使用Cookie中包含的信息来判断HTTP传输中的状态。但Cookie有自己固有的缺点：它的大小受限，大多数浏览器对Cookie大小限制为4K；Cookie机制可以在浏览器中被禁用；Cookie需要在客户端和服务器端来回地传送，繁琐且消耗带宽；存在安全风险，Cookie是以明文存放，可能被恶意客户修改，当然可以手动加密和解密Cookie，但这需要额外的编码，并且因为加密和解密需要消耗一定的时间而影响应用程序的性能。

对于HTML5，也许很有用的就是它新推出的“Web Storage”（Web 存储）API，它包括localStorage和sessionStorage，对简单的键值对（比如应用程序设置）或简单对象（如应用程序状态）进行存储，使用本地和会话存储能够很好地完成，对于存储少量的数据非常有用，但是对大量的结构化数据进行处理时，它就力所不及了，而这正是HTML5的 “Web SQL Database” API的应用所在。我们将着重讨论三个方法：
- openDatabase()：这个方法使用现有数据库或创建新数据库创建数据库对象
- transaction()：这个方法允许我们根据情况控制事务提交或回滚
- executeSql()：这个方法用于执行真实的SQL查询
<!-- more -->
### 1. 打开连接并创建数据库
```javascript
var database = openDatabase("mydb", "1.0", "test web sql database", 1024 * 1024, function() {});
```
openDatabase()方法打开一个已经存在的数据库，如果数据库不存在，它还可以创建数据库(sqlite数据库)。几个参数意义分别是：
1. 数据库名称；
2. 版本号,目前为1.0；
3. 对数据库的描述；
4. 设置数据的大小；
5. 回调函数(可省略)。

初次调用时创建数据库，以后就是建立连接了。

### 2. 建立数据表
```javascript
database.transaction(function(tx) {
    tx.executeSql(
        "CREATE TABLE IF NOT EXISTS student (id INT UNIQUE, name TEXT)", [],
        function(tx, result) {
            alert("Create table successfully.");
        },
        function(tx, error) {
            alert("Create table failed:" + error.message);
        }
    );
});
```
executeSql()函数有四个参数，其意义分别是：
1. 表示查询的字符串，使用的SQL语言是SQLite 3.6.19;
2. 插入到查询中问号所在处的字符串数据;
3. 成功时执行的回调函数。返回两个参数：tx和执行的结果;
4. 失败时执行的回调函数。返回两个参数：tx和失败的错误信息。

### 3. insert操作
```javascript
var id = Math.floor(Math.random()*1000+1);
database.transaction(function(tx) {
    tx.executeSql(
        "INSERT INTO student (id, name) values(?,?)", [id, "Jerry"],
        function(tx, result) {
            alert("Insert data successfully.");
        },
        function(tx, error) {
            alert("Insert data failed:" + error.message);
        }
    );
});
```

### 4. query操作
```javascript
database.transaction(function(tx) {
    tx.executeSql(
        "SELECT * FROM student", [],
        function(tx, result) {
            alert("Query data successfully.");
        },
        function(tx, error) {
            alert("Query data failed:" + error.message);
        }
    );
});
```
执行成功的回调函数有一参数result，其数据类型为SQLResultSet，为查询出来的数据集。
SQLResultSet 的定义为：
```javascript
interface SQLResultSet {
    readonly attribute long insertId;
    readonly attribute long rowsAffected;
    readonly attribute SQLResultSetRowList rows;
};
```
其中最重要的属性—SQLResultSetRowList 类型的 rows 是数据集的“行” 。rows有两个属性：length、item 。故获取查询结果的某一行某一列的值 ：result.rows[i].item[fieldname]

### 5. update操作
```javascript
database.transaction(function(tx) {
    tx.executeSql(
        "UPDATE student SET name=? WHERE id=?", [name, id],
        function(tx, result) {
            alert("Update data successfully.");
        },
        function(tx, error) {
            alert("Update data failed:" + error.message);
        }
    );
});
```

### 6. delete操作
```javascript
database.transaction(function(tx) {
    tx.executeSql(
        "DELETE FROM student WHERE id=?", [id],
        function(tx, result) {
            alert("Delete data successfully.");
        },
        function(tx, error) {
            alert("Delete data failed:" + error.message);
        }
    );
});
```

### 参考文章
[基于 HTML5 中的 Web SQL Database 来构建应用程序](http://www.ibm.com/developerworks/cn/web/1108_zhaifeng_websqldb/)
[HTML5开发学习（3）:本地存储之Web Sql Database（附源码）](http://www.cnblogs.com/xumingxiang/archive/2012/03/25/2416418.html)
[Web SQL Database](http://www.w3.org/TR/webdatabase/)