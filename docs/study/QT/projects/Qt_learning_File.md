---
title: Qt学习记录-文件操作
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
---

# 文件操作
新建主窗口mainWindow类
ui文件中，选择Text Edit垂直布局

主窗口的左上角menuBar新建menu"文件(&F)"，表示菜单栏。在点击后显示的分栏中action新建"新建(&N)"、"打开(&O)"、"另存为(&S)"，分别修改对象的名称为"newAction"、"openAction"、"saveAction"。
主窗口的左上角menuBar继续新建menu"编译(&E)"
主窗口的左上角menuBar继续新建menu"构建(&B)"

主程序加入MainWindow 对象主窗口的最大化
```cpp
w.showMaximized();
```

由于没有右键转到槽，需要自写槽函数进行连接

## 创建槽函数新建文件
```cpp
private slots:
    void newActionSlot();
```
槽函数实现
```cpp
void MainWindow::newActionSlot()
{
    ui->textEdit->clear(); // 清空textEdit
    this->setWindowTitle("新建文本文档.txt");
}
```

点击"newAction"时，发出信号，进行连接。
```cpp
connect(ui->newAction, &QAction::triggered, this, &MainWindow::newActionSlot);
```

## 创建槽函数打开文件
```cpp
private slots:
    void openActionSlot();
```

头文件引入#include <QFileDialog>

槽函数实现
1. 调用QFileDialog类中的getOpenFileName()函数，可打开文件对话框
2. 调用QCoreApplication类中的applicationFilePath()函数，可获取当前文件路径
3. 当获取文件名为空，做提示对话框。引入#include <QMessageBox>
4. 当获取文件名不为空，打印选中文件名。引入#include <QDebug>
5. 打开文件，创建文件对象，打开以上选择的文件，并以QIODevice只读文件。返回QByteArray ba。最后显示textEdit的文本，QString对象
6. 关闭文件

```cpp
void MainWindow::openActionSlot()
{
    // 返回QString fileName 文件名
    QString fileName = QFileDialog::getOpenFileName(this, "选择文件",
                QCoreApplication::applicationFilePath(),"*.cpp"); 
    if(fileName.isEmpty())
    {
        QMessageBox::warning(this, "警告", "请选择一个文件");
    }
    else
    {
        qDebug() << fileName;
        QFile file(fileName);
        file.open(QIODevice::ReadOnly);
        QByteArray ba = file.readAll();
        ui->textEdit->setText(QString(ba));
        file.close();
    }
}
```

点击"openAction"时，发出信号，进行连接。
```cpp
connect(ui->openAction, &QAction::triggered, this, &MainWindow::openActionSlot);
```

## 另存为文件
创建槽函数
```cpp
private slots:
    void saveActionSlot();
```
槽函数实现
1. 调用QFileDialog类中的getSaveFileName()函数，可打开文件对话框，最后一个参数为过滤器，未选择默认为所有文件类型
2. 调用QCoreApplication类中的applicationFilePath()函数，可获取当前文件路径。
3. 判断文件名是否为空，与打开文件操作类似。只写方式打开
4. 将QString 转化为 QByteArray ，写入文件
5. 关闭文件
```cpp
void MainWindow::saveActionSlot()
{
    QString fileName = QFileDialog::getSaveFileName(this,"选择文件",
                        QCoreApplication::applicationFilePath());
    if(fileName.isEmpty())
    {
        QMessageBox::warning(this, "警告", "请选择文件");
    }
    else
    {
        QFile file(fileName);
        file.open(QIODevice::WriteOnly);
        QByteArray ba;
        ba.append(ui->textEdit->toPlainText());
        file.write(ba);
        file.close();
    }
}
```

点击"saveAction"时，发出信号，进行连接。
```cpp
connect(ui->saveAction, &QAction::triggered, this, &MainWindow::saveActionSlot);
```


# 事件实现文件的保存
重写event事件函数

## 键盘按下事件
头文件中重写虚函数
引入#include <QKeyEvent>
```cpp
    // 继承QKeyEvent类
    void keyPressEvent(QKeyEvent *k); 
```
虚函数实现
1. QKeyEvent中函数modifiers()判断热键是否被按下，同时按下Ctrl和S
2. 调用保存函数
```cpp
void MainWindow::keyPressEvent(QKeyEvent *k)
{
    if(k->modifiers() == Qt::ControlModifier && k->key() == Qt::Key_S)
    {
        saveActionSlot();
    }
}
```

## 鼠标按下事件
头文件中重写虚函数
引入#include <QMouseEvent>
```cpp
    // 继承QMouseEvent类
    void mousePressEvent(QMouseEvent *m); 
```
虚函数实现
1. 获取鼠标位置QPoint 
2. QMouseEvent中函数button()判断鼠标某键被按下
```cpp
void MainWindow::mousePressEvent(QMouseEvent *m)
{
    QPoint pt = m->pos();
    qDebug() << pt;
    if(m->button() == Qt::LeftButton)
    {
        qDebug() << "左键被按下" << endl;
        
    }
    else if(m->button() == Qt::RightButton)
    {
        qDebug() << "右键被按下" << endl;
    }
}
```