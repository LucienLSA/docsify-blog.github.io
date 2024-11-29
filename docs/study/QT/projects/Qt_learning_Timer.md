---
title: Qt学习记录-定时器
katex: true
categories: 
- Qt基础学习
tags:
- Cpp
- Qt Creator
---

# QObject定时器
startTimer killTimer


## 创建定时器

新建QWidget窗口，设置PushButton，开始和暂停计时。对其设置转到槽
定义每次计时的时间
```CPP
#define TIMEOUT 1*1000
```

```
头文件中定时器事件，一个项目可定义多个定时器
```CPP
    virtual void timerEvent(QTimerEvent *event);
```
```CPP
    int myTimerId;
```

pushButton点击事件的槽函数实现
```CPP
void Widget::on_startButton_clicked()
{
    // 开启定时器，返回定时器编号
    myTimerId = this->startTimer(TIMEOUT);
}
```
事件槽函数的实现，定时器事件发生，事件ID
```CPP
void Widget::timerEvent(QTimerEvent *event)
{
    if(event->timerId() != myTimerId)
        return;
}
```

## label中插入图片

.cpp文件的构造函数中加入
```cpp
    QPixmap pix("E:\\Qt\\Test\\objectTimer\\cloud.jpg");
    ui->label->setPixmap(pix);
```
点击开始时启动定时器，需要变量记录当前是哪一张图片
头文件定义
```cpp
    int picId;
```
构造函数中初始化，并在定时器事件中设置路径和显示图片
```cpp
    picId = 2;
```
```cpp
    void Widget::timerEvent(QTimerEvent *event)
{
    if(event->timerId() != myTimerId)
        return;
    QString path("E:\\Qt\\Test\\objectTimer\\");
    path += QString::number(picId); // 将整型转化为QString类型
    path += ".jpg";

    QPixmap pix(path); // 显示图片
    ui->label->setPixmap(pix);

    picId++;
    if(picId == 4)
    {
        picId = 1;
    }
}
```

同理，定时器结束，设置槽函数
```cpp
    void Widget::on_stopButton_clicked()
{
    this->killTimer(myTimerId);
}
```

## label中的图片适配
勾选QLabel中的scaledContents
![效果](https://s2.loli.net/2024/04/05/g9NzLPokuOalhUZ.png)


# QTimer定时器
同样方式设置ui

设置pushButton的转到槽
```cpp
void Widget::on_startButton_clicked()
{
    timer->start(TIMEOUT);
}
```

头文件中加入
```cpp
    QTimer * timer;
```
构造函数中
```cpp
    timer = new QTimer;
```
## 显示图片
```cpp
    QImage img;
    img.load("路径");
    ui->label->setPixmap(QPixmap::fromImage(img));
```
定义图片编号，并初始化
```cpp
    int picId;

    picId = 2;
```

## 信号与槽
槽函数
```cpp
private slots:
    void timeoutSlot();
```
槽函数实现
```cpp
void Widget::timeoutSlot()
{
    QString path("路径");
    path += QString::number(picId);
    path += ".jpg";

    QImage img;
    img.load(path);
    ui->label->setPixmap(QPixmap::fromImage(img));

    picId++;
    if(picId == 4)
    {
        picId = 1;
    }
}
```

构造函数中创建connect
```cpp
    // 定时器时间到，发出timeout信号
    connect(timer, &QTimer::timeout, this, &Widget::timeoutSlot);
```

同样结束事件，转到槽
```cpp
void Widget::on_stopButton_clicked()
{
    timer->stop();
}
```

设置定时器只工作一次，pushButton单次运行，singleShot(时间，谁处理，槽函数);
```cpp
void Widget::on_singleButton_clicked()
{
    QTimer::singleShot(1000,this,SLOT(timeoutSlot()));
}
```