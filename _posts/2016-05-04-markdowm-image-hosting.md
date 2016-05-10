---
layout: post
title:  "在markdown中优雅的插入图片"
date:   2016-05-04 21:20:18 +0800
categories: MarkDown
tags: 图床 Typora  
author: Lyon
---
* content
{:toc}


我使用的md编辑器是**Typora**，它可以简化很多markdown语法*（这里得吐槽一下Table语法）*

**Typora**插入图片也特别方便，直接把图片拉进来即可，但如果是**本地图片**时，图片链接会写成本地的绝对路径。那么，如何优雅的在Markdown中插入图片呢？







# Markdown中插入图片

我第一时间想到的是GitHub的仓库

## GitHub

1. 将markdown需要用的图片放到git仓库中，**发布**到github上
2. 访问github仓库中那张图片，点击**Raw**按钮
3. 拷贝链接地址，在Markdown中引用图片



需求是实现了，但感觉还是有些麻烦，于是继续找解决方案

## 新浪微博

1. 把照片传微博里
2. 复制刚传的那张照片的网址
3. 拷贝链接地址，插入到markdown文档中



感觉和GitHub差不多，耗时稍微比它快一点，但还是不满足，继续找

## 图床

- [围脖图床](https://weibotuchuang.sinaapp.com/)

  - 原理应该就是微博操作 帮大家简化了

  - 安装浏览器插件即可使用

    ![](http://ww4.sinaimg.cn/large/bf341635jw1f3jp7jmquwj208a07iaa2.jpg)

​	

- [SM.MS](https://sm.ms/)
  - 可以直接拖拽图片，生成链接
  - 下面的SM.MS界面图就是用这个生成

![](https://ooo.0o0.ooo/2016/05/04/5729fd5b85041.png)



- [极简图床](http://yotuku.cn/)

  - 可以直接拖拽图片，生成链接和markdown语句，直接复制markdown语句更为方便

  - 能查看**我上传的**历史，方便多次利用图片链接

  - 可作为Chrome浏览器插件

  - 可以把公共空间设置为私人空间，不过需要注册[七牛云](https://portal.qiniu.com)(这里留个坑，打算有时间来注册试试七牛云的其他功能)

    ![](http://i2.piimg.com/82284d2fd51a997b.png)



# 结束语

最后选择了七牛云的极简图床，各方面需求都满足了，写个博客记录下

**果然爱折腾还是不错的嘛**



附知乎上相关问题：[Markdown中插入图片有什么技巧？](https://www.zhihu.com/question/21065229)

