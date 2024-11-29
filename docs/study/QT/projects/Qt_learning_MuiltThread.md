---
title: Qt学习记录-TCP的多线程
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
- TCP
- 多线程
---

在TCP客户端和服务端基础上修改，加入多线程
修改TcpServer下，cpp文件中函数
启动线程
新建C++的myThread类，继承QObject。则新增文件mythread.h和mythread.cpp

去掉服务器接受数据的函数clientInfoSlot()

修改newClientHandler()函数，保留以下代码
并加入启动线程，创建线程对象，开始线程
```cpp
void Widget::newClientHandler()
{
    QTcpSocket *socket = server->nextPendingConnection();
    ui->ipLineEdit->setText(socket->peerAddress().toString());
    ui->portLineEdit->setText(QString::number(socket->peerPort()));
    // 启动线程
    myThread *t = new myThread(socket);
    t->start();
}
```

mythread.h头文件中继承public QThread，即继承#include <QThread>
```cpp
class myThread : public QThread
```

widget.h头文件中引入#include "mythread.h"

mythread.h中加入run()函数，进行重写
```cpp
public:
    void run();
```

修改myThread的构造函数，引入头文件#include <QTcpSocket>，私有成员变量
```cpp
private:
    QTcpSocket *socket;
```
修改myThread头文件的构造函数定义，
```cpp
    explicit myThread(QTcpSocket *s );
```
构造函数实现
```cpp
myThread::myTread(QTcpSocket *s )
{
    socket = s;
}
```

启动线程后，start相当于调用myTread的run函数，服务器处理客户端，接受客户端的消息
定义槽函数
```cpp
public:
    void clientInfoSlot();
```
实现槽函数，myTread类中不能操作ui文件，多线程不能跨线程操作ui.
引入#include <QDebug>
不能在其他类里面操作界面
```cpp
void myThread::clientInfoSlot()
{
    qDebug() << socket->readAll();
}
```
run()函数重写
```cpp
void myThread::run()
{
    connect(socket, &QTcpSocket::readyRead, this, &myThread::clientInfoSlot);
}
```

运行后，在moc_widget.cpp文件中，去掉报错提示
```cpp
        case 1: _t->clientInfoSlot(); break;
```

## 运行结果
### 打开已完成编译的.exe文件时，遇到问题
Qt 5.12.2由于找不到qt5cored.dll,无法继续执行代码
### 解决方案
https://blog.csdn.net/m0_47470899/article/details/113922383

![1](https://s2.loli.net/2024/04/10/iyb4tCqLNTsXnEa.png)

在debug文件中成功打开TcpClient的客户端.exe文件，实现多线程操作，客户端向服务器传输数据，通过qDebug()打印输出
![2](https://s2.loli.net/2024/04/10/5qwtL4oWZECGrdi.png)