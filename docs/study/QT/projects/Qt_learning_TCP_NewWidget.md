---
title: Qt学习记录-TCP客户端、服务端和新建新窗口
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
- TCP
- Widget
---

# TCP客户端

# 新建Widget小窗口
创建Widget项目，文件名称为"TcpClient"
label客户端、label服务器地址、label服务器端口号，iplineEdit服务器地址文本框和portlineEdit服务器端口地址文本框
pushButton连接和取消

# 槽函数和连接
pushButton取消，可直接右键转到槽
```cpp
void Widget::on_cancelButton_clicked()
{
    this->close();
}
```
pushButton连接，同理

## TCP客户端实现
在工程文件.pro中加入network
在widget.h头文件中引入#include <QTcpSocket>
创建私有成员变量，网络对象
```cpp
    QTcpSocket *socket; 
```
并在cpp文件中初始化，创建socket对象
```cpp
    socket = new QTcpSocket; 
```

pushButton连接
1. 获取ip地址和端口号
2. 引入#include <QHostAddress>类
3. 连接服务器，将IP的QString类型转化为QHostAddress类型;将port的QString类型转化为短整型
4. 连接服务器成功，socket对象会发出信号，弹出对话框，引入#include <QMessageBox>
5. 连接异常断开，socket对象也会发出信号，弹出对话框

```cpp
void Widget::on_connectButton_clicked()
{
    QString IP = ui->ipLineEdit->text();
    QString port = ui->portLineEdit->text();

    socket->connectToHost(QHostAddress(IP), port.toShort());
    connect(socket, &QTcpSocket::connected, [this]()
    {
        QMessageBox::information(this, "连接提示", "连接服务器成功");
    });
    connect(socket, &QTcpSocket::disconnected, [this]()
    {
        QMessageBox::warning(this, "连接提示", "连接异常，网络断开");
    });
}
```


# TCP服务端

# 新建Widget小窗口
创建Widget项目，文件名称为"TcpServer"
label服务器、label客户端地址、label客户端端口号，iplineEdit客户端地址文本框和portlinEdit客户端端口地址文本框

## TCP服务器实现
在工程文件.pro中加入network
在widget.h头文件中引入#include <QTcpServer>和 #include <QTcpSocket>
创建私有成员变量，网络对象
```cpp
    QTcpServer *server; 
```
1. 在cpp文件中初始化，创建server对象
2. listen()监听，第一个参数为网卡端口号，第二个参数为
3. 客户端发起连接，server发出信号
4. accept接受连接，建立槽函数

头文件中定义端口号
```cpp
#define PORT 8000
```
cpp文件中加入初始化
```cpp
    server = new QTcpServer; 
    server->listen(QHostAddress::AnyIPv4, PORT);
    connect(server, &QTcpServer::newConnection, this, &Widget::newClientHandler);
```

```cpp
private slots:
    void newClientHandler();
```
槽函数实现
1. 建立TCP连接，返回QTcpSocket类型
2. socket中函数peerAddress()获取客户端地址，返回为QHostAddress类型，需转化为QString类型
3. socket中函数peerPort()获取客户端端口号，返回为QString类型，需转化为无符号短整型 

```cpp
void Widget::newClientHandler()
{
    QTcpSocket *socket = server->nextPendingConnection();
    ui->ipLineEdit->setText(socket->peerAddress().toString());
    ui->portLineEdit->setText(QString::number(socket->peerPort()));
}
```

# 新建窗口
## TcpClient 发送数据

加入界面文件，新建Qt的设计师界面，类名定义为Chat，界面模板(templates/forms)选择为Widget

ui文件chat中加入label输入信息、lineEdit，clearButton清空和sendButton发送

Widget::on_connectButton_clicked()函数中， 连接服务器成功后，在connect()函数中隐藏原窗口、显示Chat窗口
在函数运行结束后，如果局部变量在栈区，就会立即被释放掉，因此在堆区新建Chat类的对象
```cpp
    connect(socket, &QTcpSocket::connected, [this]()
    {
        QMessageBox::information(this, "连接提示", "连接服务器成功");
        this->hide();
        Chat *c = new Chat(socket);
        c->show();
    });
```

Chat头文件中，引入#include <QTcpSocket>
创建Chat对象，通过参数传入
```cpp
public:
    // 默认参数只能放在后面
    explicit Chat(QTcpSocket *s, QWidget *parent = 0);
```
私有成员变量 
```cpp
private:
    QTcpSocket *socket;
```

Chat头文件中更新加入的参数，修改构造函数
```cpp
    socket = s;
```

即Chat.cpp中的构造函数为
```cpp
Chat::Chat(QTcpSocket *s, QWidget *parent ) :
    QWidget(parent),
    ui(new Ui::Chat)
{
    ui->setupUi(this);
    socket = s;
}
```

右键清空，发送转到槽
```cpp
void Chat::on_clearButton_clicked()
{
    ui->lineEdit->clear();
}
```
socket中写入数据，类型为QByteArray
```cpp
void Chat::on_sendButton_clicked()
{
    QByteArray ba;
    ba.append(ui->lineEdit->text());
    socket->write(ba);
}
```

## TcpServer 接受数据
Widget的ui文件中，加入接受数据的mainLineEdit文本框

在Widget::newClientHandler()函数中

新建客户端消息的槽函数
```cpp
private slots:
    void clientInfoSlot();
```
服务器受到客户端发送的信息，socket发出readyRead信号
```cpp
void Widget::newClientHandler()
{
    QTcpSocket *socket = server->nextPendingConnection();
    ui->ipLineEdit->setText(socket->peerAddress().toString());
    ui->portLineEdit->setText(QString::number(socket->peerPort()));
    connect(socket, &QTcpSocket::readyRead, this, &Widget::clientInfoSlot);
}
```
槽函数实现
槽函数中加入sender()函数，可以获取谁发出的信号
将QByteArray类转化为QString类，并显示其结果
```cpp
void Widget::clientInfoSlot()
{
    // 获取信号的发出者
    QTcpSocket *s = (QTcpSocket *)sender(); // 强转类型
    ui->mainLineEdit->setText(QString(s->readAll()));
}
```

## 运行
输入服务器地址和端口号
![1](https://s2.loli.net/2024/04/10/RZSiu9MzUxwBQXA.png)
连接后提示连接成功
输入信息可进行数据的传送
![2](https://s2.loli.net/2024/04/10/RAigpYCnwus1VIJ.png)