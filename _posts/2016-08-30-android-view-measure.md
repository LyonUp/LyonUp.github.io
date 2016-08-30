---
layout: post
title:  "Android View的测量"
date:   2016-08-30 10:04:55 +0800
categories: Android
tags: View Measure
author: Lyon
---
* content
{:toc}

在绘制View之前，我们必须知道它的大小和位置，Android系统在绘制View之前，正是通过onMeasure()方法进行对View的测量。







# onMeasure()方法

```java
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }
```

系统使用onMeasure()方法通过借助MeasureSpec类作为参数进行View的测量，如果我们自定义View，也必须通过重写这个方法进行测量

---

# MeasureSpec类

MeasureSpec是一个32位的int值，其中高2位表示测量的模式，低30位用于存储测量的大小，并且在计算中通过位运算来提高优化效率，从MeasureSpec对象中提取具体的测试模式和大小可以通过以下代码：

```java
	int specMode = MeasureSpec.getMode(widthMeasureSpec);
    int specSize = MeasureSpec.getSize(widthMeasureSpec);
```

---

# 测量模式

- EXACTLY

  当控件的layout_width和layout_height指定为具体数值，如100dp或者match_parent时，系统使用的是这种模式，即精确值模式


- AT_MOST

  当控件layout_width和layout_height指定为warp_content时，控件大小一般随子控件大小或者自身内容变化而变化，但此时控件的尺寸不能超过父控件的最大大小

- UNSPECIFIED

  不指定其大小的测量模式，使用的很少，一般会是父控件的AdapterView通过onMeasure方法传入的模式

**View默认的测量模式是EXACTLY，如果采用了自定义View又想使用AT_MOST模式的话，必须得重写onMeasure，如果不重写，系统不知道该使用多大的尺寸，那么他会默认填充整个布局**

---

# 重写onMeasure()

查看onMeasure方法的源码，可以发现系统最后是调用setMeasuredDimension方法，所以我们重写onMeasure方法的目的就是，在onMeasure中获得测量对的尺寸，然后调用setMeasuredDimension方法，传入测量的宽高，如下代码：

```java
    // 重写测量方法 不重写的话 默认是精确模式测量
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        //super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        setMeasuredDimension(measureWidth(widthMeasureSpec),measureHeight(heightMeasureSpec));
    }
```

measureWidth是我自定义获取测量宽度的方法，主要是通过MeasureSpec获取测量模式，根据模式判断测量大小，代码如下：

```java
   private int measureWidth(int widthMeasureSpec) {
        int result = 0;
        int specMode = MeasureSpec.getMode(widthMeasureSpec);
        int specSize = MeasureSpec.getSize(widthMeasureSpec);

        // 测量为精确模式
        if (specMode == MeasureSpec.EXACTLY){
            result = specSize;
            System.out.println(""+specSize);
        }else {
            result = 200;
            if (specMode == MeasureSpec.AT_MOST){
                result = Math.min(result, specSize);
            }
        }
        return result;
    }
```

这里在AT_MOST模式下，因为使用了warp_content，必须指明测量大小，一般是遍历子控件或者自身内容，获取测量大小以包住内容，这里为了简便直接写死成200px。

---

# 结束语

- 当View测量完毕后，需要讲子View放到合适的位置，这个过程就是View的Layout过程，知道View的大小与位置后，则会调用onDraw()方法进行绘制
- ViewGroup的测量过程：当其onMeasure()在其大小为warp_content时，需要对子View进行遍历来获取所有子View的大小，从而决定自己的大小。而在其它模式下则通过具体值来设置自身大小
- ViewGroup的定位过程：ViewGroup在执行Layout过程时，同样是遍历调用子View的Layout()方法，并指定具体显示的位置，从而决定布局位置。由于Layout()被定义为final，所以在自定义ViewGroup时，通常是重写onLayout()来控制其子View显示位置的逻辑
- ViewGroup的绘制过程：通常情况下不需要绘制，如果指定了ViewGroup的背景颜色，才会调用其onDraw()方法。而ViewGroup会调用dispatchDraw()方法来遍历子View，并调用子View的绘制方法来完成绘制工作