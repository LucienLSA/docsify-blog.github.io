---
title: mysql数据库学习
katex: true
categories: 
- mysql
tags:
- linux
- mysql
---
## 数据库基础知识

### 数据

1. 数据是数值，图像、文字和数字
2. 人们将信息转化二进制数据，存储在磁盘中进行数据记录。通过文件系统管理，以文件形式显示

### 数据库

按照数据结构来组织、存储和管理数据的仓库，是一个文件或者一组文件

表是数据库中存储数据的基本单位，数据按分类存储在不同的表中，便于查询。同一指令进行增删改查

execl表难以保存，易损坏。难以进行复杂查询

## DBMS(数据库管理系统)

创建和操作数据库。数据库本质是文件信息管理，有组织的存储数据的仓库。

## 运维与数据库

### 数据库的形式

1. 自己在linux上安装，数据存储在linux机器磁盘上，运维自己管理
2. 云服务器产品（数据库安装在阿里服务器，通过账号密码，远程使用）

### 分类

#### 关系型数据库

事务一致性(ACID)，二维界面、SQL语言、完整性好、复杂查询。但硬件性能不佳、表结构固定、海量数据无法高效读取

数据表

#### 非关系型数据库

## 数据库运维和开发

运维人员，主要是对数据库架构、设计、维护

- 单实例、多实例
- SQL语句基础CURD学习、权限管理字符集、数据库引擎
- 备份方案
- 复制方案
- 高可用方案

开发人员，主要是对数据进行设计、开发

- 针对业务进行数据库设计、表结构设计
- 高性能索引
- 视图
- 存储过程
- 函数

## 数据库实践

### 特点

MySQL基于socket编写的C/S架构

客户端软件 有mysql命令、python模块

### mysql服务端和客户端

*B/S是浏览器和服务器端，客户机不需要装软件；C/S是客户端和服务器端，在客户机端必须装客户端软件及环境，才能访问服务器*

mysql服务端负责数据处理，运行在数据库服务器上。用户通过发送增删改查等请求，发送给客户端软件，然后通过网络提交请求给服务端，服务端接收到请求再进行处理，然后返回。

- 服务端、客户端可在不同机器上，也可同一个机器

## ubuntu下mysql数据库的安装

https://www.bilibili.com/video/BV12q4y1U7sZ/?spm_id_from=333.337.search-card.all.click&vd_source=917ef87e48a267f0acc88f766dea0a6e

```bash
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:

Invalid option provided.

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0

Skipping password set for root as authentication with auth_socket is used by default.
If you would like to use password authentication instead, this can be done with the "ALTER_USER" command.
See https://dev.mysql.com/doc/refman/8.0/en/alter-user.html#alter-user-password-management for more information.

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : n

 ... skipping.
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!

## mysql访问登录

### 启动mysql服务端

```bash
systemctl status mysql.service
```

### 客户端连接mysql

```bash
sudo mysql -u root -p
Enter password: 
```

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.36-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

### path变量修改

```bash
echo $PATH
```

修改profile

```bash
vim /etc/profile
# 写入PATH，加入mysql路径
export PATH=/application/mysql/bin:$PATH
```

```bash
tail -1 /etc/profile

source /etc/profile # 生效
```

### 修改密码

```bash
mysqladmin -uroot -p password
```

在管理员(admin)下，可进入mysql

## mysql操作

```sql
drop database test; # 删除test数据库
```

输入查看端口号的命令

```sql
show global variables like 'port';
```

# Windows----MySQL5.7版本下载、安装、配置与使用

## 使用ZIP压缩包进行安装

[https://blog.csdn.net/weixin_46485638/article/details/134105089]()

# Navicat16安装和激活详细讲解（全网最简单且靠谱）

[Navicat16安装和激活详细讲解（全网最简单且靠谱）_navicat 16-CSDN博客](https://blog.csdn.net/weixin_50670076/article/details/136350060)

# 如何在navicat中导入mysql数据库（即.sql后缀的数据库）

[如何在navicat中导入mysql数据库（即.sql后缀的数据库）_navicat如何导入mysql文件-CSDN博客](https://blog.csdn.net/weixin_43573126/article/details/121787860)

# Field 'id' doesn't have a default value 错误的解决办法

[https://blog.csdn.net/qq_39086076/article/details/80520672]

在navicat中操作

# CentOS7 安装下载MySQL

[Linux CentOS 7 安装mysql的两种方式_linux centos7安装mysql-CSDN博客](https://blog.csdn.net/Escorts/article/details/118941623)

[Centos7离线安装Mysql教程_cnetos 下离线mysql如何启动-CSDN博客](https://blog.csdn.net/m0_51809290/article/details/142490531)

## 安装报错

解决mariadb冲突和MySQL没有卸载干净的问题

[错误：依赖检测失败mysql-community-common(x86-64) ＞= 5.7.9 被 mysql-community-libs需要 解决centos安装mysql报错_mysql-community-common(x86-64) &gt;= 5.7.9 is needed -CSDN博客](https://blog.csdn.net/qq_35155680/article/details/115267192)

[解决 MySQL 5.7 修改密码报错：Your password does not satisfy the current policy requirements._failed! error: your password does not satisfy the -CSDN博客](https://blog.csdn.net/peng2hui1314/article/details/132001298)

[MySQL 报错：ERROR 1820 (HY000): You must reset your password using ALTER USER statement before-CSDN博客](https://blog.csdn.net/Wing_kin666/article/details/110921440)

[【MySQL】CentOS7 卸载以及安装 MySQL 详细流程_虚拟机卸载mysql-CSDN博客](https://blog.csdn.net/liuwanqing233333/article/details/128742977)

## MySQL的使用报错

[一篇文章让你解决sql报错check the manual that corresponds to your MySQL server version for the right syntax to-CSDN博客](https://blog.csdn.net/qq_44050737/article/details/117084682)
