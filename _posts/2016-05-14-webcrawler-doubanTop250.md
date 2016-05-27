---
layout: post
title:  "Python网络爬虫之豆瓣Top250"
date:   2016-05-14 23:31:18 +0800
categories: Python
tags: WebCrawler 豆瓣  
author: Lyon
---
* content
{:toc}

第一次网络爬虫的尝试，由于比较喜欢电影，这一次的爬取目标定于[豆瓣电影TOP250](https://movie.douban.com/top250) 。在知乎上看到[zlxd](https://www.zhihu.com/people/xlzd)发表的Web crawler with Python系列文章[*点这跳转*](https://www.zhihu.com/people/xlzd/posts)，跟着他进行了我的第一次python爬虫之旅，在这篇博客里，我简单回顾下。







# Python网络爬虫

## 个人环境说明

- python版本为3.5.1
- 使用的IDE为PyCharm2016.1.3
- 导入了requests 、beautifulsoup4 、codecs三个module



## 分析豆瓣电影TOP250

1. 思考爬虫需要干什么
   - 爬取豆瓣电影TOP250的电影名
2. 目标网站的特点
   - 信息未隐藏 **（ctrl + u查看源代码后 发现数据就在里面)**
3. 选择合适的架构
   - 鉴于还不知道有哪些框架 这里采用BeautifulSoup来解析HTML源码



##  开始写python代码

### 完成下载网页源码

先通过cmd运行pip install requests 来安装requests。


```python
import requests

DOWNLOAD_URL = 'http://movie.douban.com/top250'

def download_page(url):
    data =requests.get(url).content
    return data

if __name__ == '__main__':
    print download_page(DOWNLOAD_URL)
```
接下来，你会发现在PyCharm中找不到import的requests，这是因为没有在当前project配置，点击【File】->【Settings】-> 【project Interpreter】中的 **绿色+号**进行添加requests。

然后简单的代码和配置后，我很成功的在PyCharm中输出了网页的源码。

如果你们得到的是以下的运行结果:

```python
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>dae</center>
</body>
</html>
```

说明豆瓣（也就是需要登录的网站）没有登录或者被服务器识别为了爬虫程序而拒绝了访问，为了我们能成功登录，这里需要伪装成浏览器，由于默认的User-Agent是python-requests/2.10.0（后面的数字为版本号），我们需要修改请求头——User-Agent：

```python
def download_page(url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_2) 		AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.80 Safari/537.36'
    }
    data = requests.get(url, headers=headers).content
    return data
```

每个人浏览器User Agent可能都不一样，大家可以直接google[User-Agent](http://whatsmyuseragent.com/)来获得，当然直接使用上面的代码也是可以的。



## 解析下载到HTML源码

我们先在豆瓣电影TOP250，随便选中一个电影的名字，右键点击【检查】，在开发者工具中可以看到以下界面：

![](http://i4.buimg.com/158788235323c04d.png)

我们所爬取数据的层次就显而易见了:

- ol calss='gird_view'
  - li
    - div class='hd'
      - span class='title'

通过cmd运行pip install beautifulsoup4来安装BeautifulSoup。

```python
 from bs4 import BeautifulSoup
  
  def parse_html(html):
  
      soup = BeautifulSoup(html)
  
      movie_list_soup = soup.find('ol', attrs={'class': 'grid_view'})
 	  movie_name_list = []
      for movie_li in movie_list_soup.find_all('li'):

         detail = movie_li.find('div', attrs={'class': 'hd'})
         movie_name = detail.find('span', attrs={'class': 'title'}).getText()
         movie_name_list.append(movie_name)

```

这样我们就解析了一页的数据



![](http://i4.buimg.com/bdd2804db7955f74.png)

在【后页】点击【检查】，在开发者工具可以看到了以下界面：

![](http://i4.buimg.com/1f08742c1fdce852.png)

也就是 span  calss='next'   -> a href ,于是有了下面的代码：

```python 
next_page = soup.find('span', attrs={'class': 'next'}).find('a')
```



然后我们需要在解析HTML源码的方法里，判断有无后页，并进行循环解析，这里采用递归：

```python
if next_page:
        return movie_name_list, DOWNLOAD_URL + next_page['href']
    return movie_name_list, None
```



这里的意图也很明显，返回两个值，一个是电影名列表，一个是下页的链接，我们只需要判断下页链接是否为空就能知道解析的结束：

```python
while url:
    html = download_page(url)
    movies, url = parse_html(html)
```

注意，因为movies是在while里面，其实最后movies只是存了最后一页的电影名，我们需要再加一步，在每次while时，把movies写入文件中。



## 把得到的数据写入文件

```python
import codecs
with codecs.open('movies', 'wb', encoding='utf-8') as fp:
    while url:
        html = download_page(url)
        movies, url = parse_html(html)         		 
        fp.write(u'{movies}\n'.format(movies='\n'.join(movies)))
```



## 整体思路

```python
import something 

#下载源码
def download_page(url)   
#解析源码
def parse_html(html)
#数据存储到文件
def main()

# 程序入口
if __name__ == '__main__':
    main()

```

最后全部的代码在我的Gist里的[**moviesTop250.py**](https://gist.github.com/LyonUp/cc10ceae37e4473db7805fadc4fd13a7)



# 结束语

我的[Gist](https://gist.github.com/LyonUp)中还顺带写了一个booksTop250的爬虫，大家可以自己试试能不能独立写出来，思路是一样的。