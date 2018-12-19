---
title: 一个windows电脑安装两个mysql服务
date: 2018-12-19 18:37:54
tags:
---

首先唠叨下 **如何在windows电脑下配置免安装版mysql5.7**

第一步肯定是去官网（[传送门](https://dev.mysql.com/downloads/mysql/)）下个zip包啊，解压~  
第二步在解压的根目录建一个my.ini文件，拷贝以下的代码进去 

```
[client]
port=3306
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\mysql-5.7.24-winx64-slave
# 设置mysql数据库的数据的存放目录
datadir=D:\\mysql-5.7.24-winx64-slave\\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

**注意**：要将**basedir**和**datadir**修改成你的mysql相应的路径。
第三步使用管理员运行cmd，在bin目录下输入: **mysqld （--defaults-file=xxx/my.ini）  --initialize --console**。这里做一下解析

```
--defaults-file //指定配置文件（亲测，不指定my.ini，也会默认指向它，注意是my.ini）
--initialize //创建数据文件目录和mysql系统数据库 产生随机root密码
--console //在cmd窗口可以输出错误日志（不然就要到data里面的xxx.err文件找）
```
第四步就是安装了（又说免安装？？？现在是安装服务，不是软件）,在bin目录下输入：**mysqld install**  
第五步 启动。输入：**net start mysql**。  
第一个mysql就这样安装完了~   

----------------------**这是一条认真的分割线**------------------------  

然后说说第二个~  
第一步拷贝第一个已安装好的mysql目录，改成别的名字如mysql2，并且把data目录整个删掉。  
第二步修改my.ini，以下为我自己修改过的（只需要该两个**端口号**和两个路径**basedir**和**datadir**）

```
[client]
port=3307
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
# 设置3307端口
port=3307
# 设置mysql的安装目录
basedir=D:\\mysql-5.7.24-winx64-master
# 设置mysql数据库的数据的存放目录
datadir=D:\\mysql-5.7.24-winx64-master\\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```
第三步 同上  
第四步 要改成输入：**mysqld install 服务名**。因为不能两个服务名一样嘛，服务名随意一个mysql2也行了~
第五步 也改成: **net start 服务名**（刚刚第四步的那个）。如：net start mysql2

好了，本教程完毕。这个教程已经说得非常清楚了，还不懂的话，可能是要重启试试了。。。

-------------
其他可能会用到的命令
删除服务命令：sc.exe delete 服务名


