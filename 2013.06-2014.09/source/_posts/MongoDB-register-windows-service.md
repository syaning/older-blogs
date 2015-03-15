title: 将MongoDB注册为Windows服务
date: 2014-09-20 15:43:14
categories: DB
tags:
---
以管理员权限运行命令行，在MongoDB安装目录的bin目录下，执行：
```
mongod --install --serviceName MongoDB --serviceDisplayName MongoDB --logpath "D:\Program Files\MongoDB 2.6 Standard\log\mongodb.log" --dbpath "D:\Program Files\MongoDB 2.6 Standard\data" --directoryperdb
```
其中logpath指定日志文件存放位置，dbpath指定数据库数据文件存放位置，directoryperdb表示为每个数据库生成一个文件夹。

### 参考文章
[MongoDB安装为Windows服务方法与注意事项](http://blog.csdn.net/chaijunkun/article/details/7227967)