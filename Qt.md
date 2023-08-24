## 01 第一个程序
```C++
// pro 后缀的文件
#-------------------------------------------------
#
# Project created by QtCreator 2023-08-24T21:01:26
#
#-------------------------------------------------

QT       += core gui  // Qt包含的模块

// 大于4版本时，将会包含widget模块。4版本以前widget是gui模块的内容
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets 

TARGET = 01_FirstProject  // 目标  生成的exe程序的名称
TEMPLATE = app  // 模版  应用程序模板


SOURCES += main.cpp\  // 源文件
        widget.cpp

HEADERS  += widget.h  // 头文件
```

```C++
#ifndef WIDGET_H
#define WIDGET_H  // 等效与 #pragma once

#include <QWidget>

class Widget : public QWidget
{
    Q_OBJECT  // Q_OBJECT宏，允许类中使用信号和槽的机制

public:
    Widget(QWidget *parent = 0);  
    ~Widget();
};

#endif // WIDGET_H

```
```C++
#include "widget.h"

// 命名规范
// 类名 首字母大写，单词直接首字母大小
// 函数名 首字母小写，单词之间首字母小写

// 快捷键
// 注释  ctrl + /
// 运行 ctrl + r
// 编译 ctrl + b
// 字体缩放 ctrl + 鼠标滚轮
// 查找 ctrl + f
// 整行移动 ctrl + shift + ↑/↓
// 帮助文档 F1 （或者用Qt助手）
// 自动对齐 ctrl + i
// 同名之间的.h和.cpp切换  F4

// 帮助文档 第一种
Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
}

Widget::~Widget()
{

}
```
```C++
#include "widget.h"
#include <QApplication>  // 包含一个应用程序类的头文件

int main(int argc, char *argv[])
{
    // a是一个应用程序对象，在Qt中，应用程序对象有且仅有一个
    QApplication a(argc, argv);
    // 窗口对象Widget父类 -> QWidget
    Widget w;
    // 窗口对象默认不会显示，必须调用show方法显示窗口
    w.show();
    // 让应用程序对象进入消息循环机制（让程序不会一闪而过）
    return a.exec();
}

```

## 02 QPushButton创建
```C++
#include "widget.h"

Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    // 创建第一个按钮
    QPushButton* btn1 = new QPushButton();
    // btn->show();
    btn1->setParent(this);
    // 显示文本
    btn1->setText("第一个按钮");
    btn1->move(100, 0);

    // 创建第二个按钮
    QPushButton* btn2 = new QPushButton("第二个按钮", this);
    btn2->move(100, 100);
    btn2->resize(100, 100);

    // 重置窗口大小
    resize(600, 400);

    // 设置固定窗口大小
    setFixedSize(600, 400);

    // 设置窗口标题
    setWindowTitle("第一个窗口");
}

Widget::~Widget()
{

}
```

## 03 对象树
- 在创建QObject对象时，可以提供一个其父对象，我们创建的这个QObject会自动添加到其父对象的children()列表
- 当父对象析构的时候，这个列表中的所有对象也会被析构（注意，这里的父对象并不是继承意义上的父类！）
- 一定程度上简化了内存回收机制

```C++
#include "widget.h"
#include "mypushbutton.h"
#include <QDebug>

Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    // 创建一个自己的按钮对象
    MyPushButton* myBtn = new MyPushButton;
    myBtn->setText("我自己的按钮");
    myBtn->move(100, 200);
    myBtn->setParent(this);
}

// 先打印对象，子类还没有释放，所以打印顺序与析构顺序相反
Widget::~Widget()
{
    qDebug() << "Widget析构函数调用";
}

```
```C++
#ifndef MYPUSHBUTTON_H
#define MYPUSHBUTTON_H

#include <QPushButton>

class MyPushButton : public QPushButton
{
    Q_OBJECT
public:
    explicit MyPushButton(QWidget *parent = 0);
    ~MyPushButton();

signals:

public slots:
};

#endif // MYPUSHBUTTON_H

```
```C++
#include "mypushbutton.h"
#include <QDebug>  // 用于打印调试信息

MyPushButton::MyPushButton(QWidget *parent) : QPushButton(parent)
{
    qDebug() << "MyPushButton构造函数调用";
}

MyPushButton::~MyPushButton()
{
    qDebug() << "MyPushButton析构函数调用";
}

```
## 04 信号和槽
- ``` connect(信号的发送者, 发送的具体信号, 信号的接受者, 信号的处理(槽)) ```
- 信号和槽的优点：松散耦合，信号**发送端**和**接收端**本身没有关联，通过connect连接，将两端耦合在一起
```C++
// QT内置的信号和槽
Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    // 创建一个自己的按钮对象
    MyPushButton* myBtn = new MyPushButton;
    myBtn->setText("我自己的按钮");
    myBtn->move(100, 200);
    myBtn->setParent(this);

    // 信号和槽
    connect(myBtn, &MyPushButton::clicked, this, &Widget::close);
}
```

```C++
// 自定义的信号和槽
```

```C++

```









































