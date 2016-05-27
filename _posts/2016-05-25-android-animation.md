---
layout: post
title:  "Android动画初窥"
date:   2016-05-25 22:39:18 +0800
categories: Android
tags: AndroidStudio Animation
author: Lyon
---
* content
{:toc}
  Android动画播放技术主要分两种，也就是**补间动画**和**帧动画**。这几天正好看完《轻松学Android开发》，特别基础的一本书。在*3.5动画播放技术*那章，我遇到了一个问题：在android stuio写的一个简单的帧动画，第一帧一直不消失，于是造成了重叠的效果（仅第一帧一直不消失，其他正常）。简单记录下动画和我的解决过程。










# Android Animation

动画实现可以采用JavaCode*（也就是New一个Animation的实例，进行相关操作）*和定义xml文件然后加载它，这里我们采用第二种方法，也就是我先在res/anim定义好xx.xml，然后使用如下代码加载：

```java
// 加载补间动画
Animation anim_tween = AnimationUtils.loadAnimation(MainActivity.this, R.anim.tween);
imageView = (ImageView) findViewById(R.id.image);        
imageView.startAnimation(anim_tween);

//开始帧动画
images.setImageResource(R.drawable.frame);
AnimationDrawable drawable = (AnimationDrawable)images.getDrawable();
drawable.start();
```

接下来我们讲下怎么写动画的xml文件



## Tween Animation

也就是补间动画，是指对场景里的对象不断进行图像变换来产生动画效果，可以实现对象的旋转、平移、缩放、渐变等。

### alpha

```xml
    <alpha
        android:fromAlpha="1.0"  
        android:toAlpha="0.0"
        android:duration="10000"
        />
```

- fromAlpha变换的起始透明度


- toAlpha终止时的透明度，取值为0.0~1.0，0.0代表全透明


- duration代表动画持续时长

### scale

```xml
    <scale
        android:fromXScale="1.0"
        android:toXScale="0.5"
        android:fromYScale="1.0"
        android:toYScale="0.5"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="6000"
        ></scale>
```

- pivotX(Y)是用于指定变换中心
- 1.0表示原始尺寸

### translate

```xml
    <translate
        android:fromXDelta="0"
        android:toXDelta="300"
        android:fromYDelta="0"
        android:toYDelta="300"
        android:duration="5000"
        ></translate>
```

- 注明移动的起始和终止的X、Y
- 属性值不加后缀，如现在的0、300，是代表绝对值
- 属性以“%”结尾代表相对于自身比例
- 属性以“%p”结尾代表相对于父控件比例

### rotate

```xml
  <rotate
        android:fromDegrees="0"
        android:toDegrees="+360"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="5000"
        ></rotate>
```

- 指明旋转位置和结束位置，单位为角度



## Frame Animation

也就是如同电影一样，通过顺序播放一系列事先加载的静态图片产生动画效果。

注意androidstudio需要在**drawable**目录下定义一个xml文件（而不是anim）

例如：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:duration="150" android:drawable="@drawable/run_anim1"/>
    <item android:duration="150" android:drawable="@drawable/run_anim2"/>
    <item android:duration="150" android:drawable="@drawable/run_anim3"/>
    <item android:duration="150" android:drawable="@drawable/run_anim4"/>
    <item android:duration="150" android:drawable="@drawable/run_anim5"/>

</animation-list>
```

- drawable图片帧的引用
- duration帧停留时间
- visible图片帧是否可见



## 结束语

其实播放帧动画时第一帧一直不消失，是因为书上教我写的代码是这样的：

```java
images.setBackgroundResource(R.drawable.frame);
AnimationDrawable drawable = (AnimationDrawable)images.getBackground();
drawable.start();
```

简单的百度了下，发现有人和我错的一样也没解决：[android studio 做了个帧动画，但是第一帧的图片一直不消失，求教](http://zhidao.baidu.com/link?url=dbhBGtWccDd6olXL6VPun8btgWy_PXUgnfw2Nd0Yf_Ddgg0tg-igOJdsOo4SojRj1FMX8R054Ky7lamr9kwnY2nXx-76jc9rBx4PJSEipRi)。于是就自己稍微思考了下，由于我很不理解为什么得先把那些静态图片设置成Background，所以第一反应是出在这里，我想了想Background是显示一张图片当背景，正好符合第一帧图片一直不消失，所以把它改成了ImageResource，也就是前面的：

```java
images.setImageResource(R.drawable.frame);
AnimationDrawable drawable = (AnimationDrawable)images.getDrawable();
drawable.start();
```

然后就神奇的好了。。



最后，一个简单的效果图：
 ![a](https://raw.githubusercontent.com/LyonUp/LyonUp.github.io/master/gif/a.gif)




