---
title: Qt学习记录-信号与槽
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
---


# 创建小窗口Qt-Widget
新建时在Detials->Class Information中选择QWidget

# 信号与槽
## 1
点击转到槽，选择信号为clicked。 自动在cpp文件中生成on_commitButton_clicked函数，还增加了private slots的槽函数
```cpp
void Widget::on_commitButton_clicked()
{
}
```
```cpp
void Widget::on_commitButton_clicked()
```
启动新的进程需引入
```cpp
#include <QProcess>
```

在文档中可找到QProcess相关使用，创建process对象
```cpp
void Widget::on_commitButton_clicked()
{
    QProcess * myProcess = new QProcess(this);
    myProcess->start(program);
}
```

获取lineedit数据
通过ui指针获取文本输入框lineedit的内容，返回QString类型
```cpp
QString program = ui->cmdLineEdit->text();
```
运行时，在lineEdit输入框中输入指令，只有在系统盘的system32中的.exe文件可执行。

lineEdit中输入完成后，"确认"触发事件以按键方式执行。在Widget的构造函数中加入连接信号与槽。
输入框发出回车信号，this指向为Widget，处理on_commitButton_clicked()函数
```cpp
// 连接信号与槽 谁发出信号，发出什么信号，谁处理信号，怎么处理
connect(ui->cmdLineEdit, SIGNAL(returnPressed()),this, SLOT(on_commitButton_clicked()));
```

## 2
也可自行添加槽函数和信号，进行连接
```cpp
// 也可使用取地址方式，发出信号和处理信号
connect(ui->cancelButton, &QPushButton::clicked,this, &Widget::on_cancelButton_clicked);
```

## 3
槽函数中处理项目不多，可使用**lambda表达式**。对浏览(browse)按钮进行设置
头文件中引入消息对话框
```cpp
#include <QMessageBox>
```
```cpp
    connect(ui->browseButton, &QPushButton::clicked,[this]()
    {
        QMessageBox::information(this, "信息","点击浏览");
    });
```








