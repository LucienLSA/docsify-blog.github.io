---
title: Qt学习记录-TCP的多线程中自定义信号
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
- TCP
- 多线程
---

在Tcp服务端和客户端多线程中，实现并发服务器，最后通过qDebug()打印传输的数据
**Qt中不建议将UI对象(例如QWidget或UI)传递给自定义线程类使用，因为跨线程访问UI对象可能会引发多线程问题，如竞争条件和线程不安全的访问**
# 自定义信号，显示在界面上

窗口是一个对象w，线程也是一个对象t，将t对象中的数据发送到w对象里面。将读出来的信息QByteArray进行发送

mythread.cpp文件中函数clientInfoSlot()进行修改
在mythread.h文件中新增signals
```cpp
signals:
    void sendToWidget(QByteArray b);
```
注释
```cpp
void Widget::clientInfoSlot()
{
    
}
```
增加发送信号
```cpp
void myThread::clientInfoSlot()
{
    QByteArray ba = socket->readAll();
    emit sendToWidget(ba);
}
```
发送信号后，在Widget中的newClientHandler()函数中接受信号，进行连接
新建widget的槽函数
```cpp
    void threadSlot(QByteArray b);
```
实现槽函数，并将数据类型转换成QString对象
```cpp
void Widget::threadSlot(QByteArray b)
{
    ui->mainLineEdit->setText(QString(b));
}
```
Widget::newClientHandler()函数中增加连接函数
```cpp
connect(t, &myThread::sendToWidget, this, &Widget::threadSlot);
```