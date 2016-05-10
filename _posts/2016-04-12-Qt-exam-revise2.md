---
layout: post
title:  "QT考试复习（二）"
date:   2016-04-12 14:14:54
categories: Exam
tags: Qt考试 Qt 
author: Lyon
---
* content
{:toc}
面向某次考试给同学们做的复习资料，这部分是针对于Qt编程，Qt的内容就只是涉及了考试要考的知识点，比较简单，放博客暂时凑文章数，目前算放个坑，以后会针对Qt写一些详细的博客。





# Qt基础

***

## 容器类（QList、QMap）

***

QList<T> 

> 是迄今为止最常用的容器类，它存储给定数据类型T的一列数值。

列表追加函数 QList::append()和QList::prepend()

中间插入函数 QList::insert()

QList<T>维护了一个指针数组，该数组存储的指针指向QList<T>存储的列表项的内容

- 如果T是一个指针类型或者指针大小的基本类型，直接存储
- 如果存储的是对象的指针，则该指针指向实际存储的对象

```c++
#include <QDebug>
int main(int argc,char *argv[])
{
  QList<QString> list;
  QString str("Hello");
  list<<str;          //插入到QList里面
  qDebug()<<list[0]<<"Wrold!";
  return 0;
}
```

对列表向前遍历：

- QListIterator<T>::toBack() 移动到最后一个列表项后面
- QListIterator<T>::hasPrevious() 检查是否有前一个
- QListIterator<T>::previous() 移动到前一个



QMap<Key,T>

> 提供一个从类型为key的键到类型为T的值的映射。

在QMap中插入、遍历、修改

```c++
#include <QDebug>
int main(int argc,char *argv[])
{
  QMap<QString,QString> map;
  map.insert("beijing","111");  //插入
  map.insert("shanghai","222");
  
  QMapIterator<QString,QString> i(map);  //只读迭代器i 
  
  for(;i.hasNext();)
    qDebug<<" "<<i.key()<<" "<<i.next().value(); //遍历
  
  QMutableMapIterator<QString,QString> mi(map); //可写迭代器mi
  if(mi.findNext("111"))
    mi.setValue("010");    //查找值Value且修改
  QMapIterator<QString,QString> modi(map);  //只读迭代器modi
  qDebug()<<" ";
  for(;modi.hasNext();)
    qDebug()<<" "<<modi.key()<<" "<<modi.next().value();
  return 0;
}
```

***

## 控件QPushButton和QLabel

***

PushButton：按钮。

```c++
QPushButton *quit = new QPushButton("Quit",this);
quit->setFont(QFont("Times",18,QFnot::Bold));
connect(quit,SIGNAL(clicked()),qApp,SLOT(quit()));
```



Label：标签。

```c++
Qlabel * datalabel = new Qlabel();
datalabel->setText("MyLabel");
datalabel->show();
```



***

## QTime和QTimer

***

获取系统当前时间

```c++
QdateTime *datatime = new QdateTime(QdateTime::currentDateTime());
datatime->date().toString();   //日期和时间部分
```

定时器

```c++
QTimer *time_clock = new QTimer(parent);   //新建定时器
connect(time_clock,SIGNAL(timeout()),this,SLOT(slottimedone())); //连接信号和槽
time_clock->start(2000); //两秒开启定时器 可重复触发
time_clock->setSingleShot(true); //仅启动一次定时器
time_clock->stop(); //停止定时器
```

***

## 文件和颜色对话框

***

文件对话框代码

```c++
QString s = QFileDialog::getOpenFileName
  (this,                    //对话框父窗口
   "open file dialog",		//对话框标题
   "/",                     //默认打开路径                  
   "C++ files(*.cpp):: C files(*.c)::Head files(*.h)");//过滤器
```

颜色对话框代码

```c++
QColor c = QColorDialog::getColor(Qt::blue); //参数是默认颜色
if(c.isValid())    //判断用户选择的颜色是否有效
  colorFrame->setPalette(QPalette(c));  //设置框体颜色
```

***

## 进度条

***

- QProgressBar   
- QProgressDialog          


***

## 布局管理

***

- 垂直布局 Vertical Layout
- 水平布局 Horizontal Layout
- 网格布局 Grid Layout
- 表格布局 Form Layout

***

## 空间间隔

***

- 水平间隔 Horizontal Spacer
- 垂直间隔 Vertical Spacer

***

## QTableWidget     

***

- 详见老师给的txt文件