---
title: Qt学习记录-MySQL数据库
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
- MySQL
---

# MySQL 8.0 Command Line

## 创建数据库

```bash
create database mydatabase; 

use mydatabase;
```

## 创建数据表
```bash
create table student (
    id integer unsigned primary key,
    name varchar(16) not null,
    birth date
)charset utf8; 
```
 
## 
```bash
select * from student;
```

# Qt工程
## 新建mysql项目
## 选择Widget类
ui文件中加入学号、姓名和生日的label。idLineEdit、nameLineEdit、birthLineEdit
findPushButton查询和insertPushButton插入

mysql.pro中加入sql

头文件中引入#include <QSqlDatabase>，创建数据库对象
```cpp
    QSqlDatabase db;
```

## 构造函数连接数据库
加载MySQL数据库驱动
```cpp
    db = QSqlDatabase::addDatabase("QMYSQL");
```
设置数据库，进行连接
打开成功，引入#include <QMessageBox>
```cpp
    db.setDatabaseName("mydatabase");
    db.setHostName("localhost");
    db.setUserName("root");
    db.setPassword("root");

    if(db.open())
    {
        QMessageBox::information(this, "连接提示", "连接成功");
    }
    else
    {
        QMessageBox::warning(this, "连接提示", "连接失败");
    }
```
右键插入转到槽
包含头文件#include <QSqlQuery>
```cpp
void Widget::on_insertPushButton_clicked()
{
    QString id = ui->idLineEdit->text();
    QString name = ui->nameLineEdit->text();
    QString birth = ui->birthLineEdit->text();

    QString sql = QString("insert into student values (%1, '%2', '%3');").arg(id).arg(name).arg(birth);
    // QString sql("insert into student values (1, "tom", "1999-10-9");");

    QSqlQuery query;
    if(query.exec(sql))
    {
        QMessageBox::information(this, "插入提示", "插入成功");
    }
    else
    {
        QMessageBox::information(this, "插入提示", "插入失败");
    }
}
```

## 插入数据库操作
右键插入的槽函数中
```bash
insert into student values (1, "tom", "1999-10-9");
```

右键查询转到槽
引入#include <QDebug>
```cpp
void Widget::on_findPushButton_clicked()
{
    QSqlQuery query;
    query.exec("select * from student;");
    while(query.next())
    {
        qDebug() << query.value(0);
        qDebug() << query.value(1);
        qDebug() << query.value(2);
    }
}
```

## 注意：若编译，加载数据库出错
解决方案：将MySQL的安装目录下的MySQL Server的lib的libmysql.dll文件，复制到qt文件中的bin