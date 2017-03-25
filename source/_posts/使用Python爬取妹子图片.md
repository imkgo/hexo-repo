title: 使用Python爬取妹子图片
date: 2017-03-25 14:44:16
tags: [Python, 爬虫]
---
> 某天看了一个爬虫的博客，发现了这么一个网址：http://www.zngirls.com/ 。

![image](http://t1.onvshen.com:8080/gallery/24112/22261/s/009.jpg)

<!-- more -->
## 分析一下网页结构

![image](http://od178u7fa.bkt.clouddn.com/Snip20170325_5.png)
点进第一个图片之后显示是这样子的,查看源码：
``` html
<a class="galleryli_link" href="/g/22262/">
    <img alt="涂雅君- 清新甜美笑容甜到快要蛀牙了" data-original="http://t1.onvshen.com:8080/gallery/24113/22262/cover/0.jpg" title="涂雅君- 清新甜美笑容甜到快要蛀牙了" src="http://t1.onvshen.com:8080/gallery/24113/22262/cover/0.jpg" style="display: inline;">
</a>
```
这个页面的下一页按钮源码是这样的：
``` html
<a href="/gallery/2.html"><span></span>下一页</a>
```
![image](http://od178u7fa.bkt.clouddn.com/Snip20170325_9.png)
看这个页面源码:
``` html
<ul id="hgallery">
    <img src="http://t1.onvshen.com:8080/gallery/24113/22262/s/005.jpg" alt="涂雅君- 清新甜美笑容甜到快要蛀牙了_5" title="点击查看高清尺寸原图">
    <img src="http://t1.onvshen.com:8080/gallery/24113/22262/s/006.jpg" alt="涂雅君- 清新甜美笑容甜到快要蛀牙了_6" title="点击查看高清尺寸原图">
    <img src="http://t1.onvshen.com:8080/gallery/24113/22262/s/007.jpg" alt="涂雅君- 清新甜美笑容甜到快要蛀牙了_7" title="点击查看高清尺寸原图">
</ul>
```
可以发现有一个id为hgallery的ul标签，ul标签里面是多个img标签，img标签的src属性就是我们需要的图片地址。直接拿到这个地址就可以获取我们想要的图片了。为了获取更多的图片，需要去翻到下一页，分析下一页，它的源码是这样的:
``` html
<a class="a1" href="/g/22262">下一页</a>
```

## 技能准备
> Python是目前一种非常流行计算机编程语言了，学习起来简单，可以做很多自己想不到的有意思的事情。Python也是一个非常适合写爬虫的语言，Python有很多第三方实现的模块，大大减少了代码量，本次使用Python来写这个爬虫。

### Requests模块
![image](http://docs.python-requests.org/zh_CN/latest/_static/requests-sidebar.png)

在处理http请求的时候，虽然Python内部已经有一些模块支持了，不过写起来还是比较麻烦，这里我们使用Requests模块。具体文档参考：http://docs.python-requests.org/zh_CN/latest/index.html

### BeautifulSoup模块
Beautiful Soup 是一个可以从HTML或XML文件中提取数据的Python库。它能够通过你喜欢的转换器实现惯用的文档导航，查找，修改文档的方式。Beautiful Soup会帮你节省数小时甚至数天的工作时间。文档地址：http://beautifulsoup.readthedocs.io/zh_CN/latest/

### threadpool模块
Python的一个线程池实现，通过http访问图片相对来说还是比较慢的，单个线程去爬图片效率非常非常低。所以我们需要引入多线程来做这个事情，threadpool就是一个很方便的工具，帮助我们完成多线程的功能。

## 实现代码
从页面中提取出来图片的地址列表，soup参数为BeautifulSoup的一个对象，又HTML页面源码创建而来。
``` Python
def list_img_url(soup):
    ul = soup.find(id="hgallery")
    img_urls = []
    for img in ul.contents:
        img_urls.append(img.attrs["src"])
    return img_urls
```

获取下一页的HTML页面地址
``` Python
def next_page(soup):
    a = soup.find("a", text="下一页")

    href = a.attrs["href"]
    return host + href
```

获取相册地址
``` Python
soup = BeautifulSoup(content, "html5lib")
a_list = soup.find_all("a", class_="galleryli_link")
```

### 完整代码
``` Python
# -*- coding: UTF-8 -*-
import requests
import uuid
from bs4 import BeautifulSoup
import threadpool

main_url_set = ([])

host = "http://www.zngirls.com"

img_page_url_set = ([])
g_page_url_set = ([])


def save_img(url):
    filepath = "/Users/mac/zn/" + str(uuid.uuid4()) + url[url.rindex("."):]
    try:
        res = requests.get(url, timeout=20)
        with open(filepath, 'wb') as f:
            f.write(res.content)
    except:
        print("下载图片异常:" + url)


def next_page(soup):
    a = soup.find("a", text="下一页")

    href = a.attrs["href"]
    return host + href


def list_img_url(soup):
    ul = soup.find(id="hgallery")
    img_urls = []
    for img in ul.contents:
        img_urls.append(img.attrs["src"])
    return img_urls


def g_page(url):
    img_page_url_set.append(url)

    try:
        html = requests.get(url, timeout=10)
    except:
        print("请求" + url + "超时")
    content = html.content.decode()
    soup = BeautifulSoup(content, "html5lib")
    img_url_list = list_img_url(soup)

    reqs = threadpool.makeRequests(save_img, img_url_list)
    pool = threadpool.ThreadPool(5)
    [pool.putRequest(req) for req in reqs]
    pool.wait()

    next_url = next_page(soup)
    if next_url in img_page_url_set:
        return
    else:
        print(next_url)
        g_page(next_url)


def next_g_page_url(soup):
    div = soup.find("div", class_="pagesYY")
    child_div_list = div.children
    for child_div in child_div_list:
        a_list = child_div.children
        for a in a_list:
            if a.contents[1] == "下一页":
                return host + a.attrs["href"]
    return ""


def run_g_page(girl_url):
    g_page_url_set.append(girl_url)
    try:
        html = requests.get(girl_url, timeout=10)
    except:
        print("请求" + girl_url + "异常")
    content = html.content.decode()
    soup = BeautifulSoup(content, "html5lib")
    a_list = soup.find_all("a", class_="galleryli_link")
    for a in a_list:
        g_page_url = host + a.attrs["href"]
        g_page(g_page_url)

    next_url = next_g_page_url(soup)
    if next_url in g_page_url_set:
        run_g_page(next_url)


if __name__ == '__main__':
    run_g_page("http://www.zngirls.com/gallery/dachidu/")

```

## 待改进...
http请求的时候可能会出现超时的情况，有些处理了，有些还没处理，可能导致爬取动作中断。可能存在重复的图片没有过滤。只在最后下载图片的时候使用了多线程，效率还可以进一步优化。对于网站的每个页面的名称含义不是很清晰，所以代码命名和注释方面不是很清晰，比较难懂。