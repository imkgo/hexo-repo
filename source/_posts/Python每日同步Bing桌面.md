title: Python每日同步Bing桌面
date: 2016-11-23 19:25:03
tags: [Python]
---
*微软Bing搜索网站每天都切换一张精美的图片作为背景，很多人都想使用Bing的背景图片作为桌面背景，网上也可以找到一些软件来每天同步Bing壁纸作为桌面。笔者不是很喜欢在电脑上每天开着这样的软件来完成切换桌面的功能，后来想是否可以通过Python写一个简单的程序来实现同样的任务呢，在网上搜索了一下，有这样想法的人还是有很多的，借鉴了很多网友的资料，实现了这样的功能。*

![images](http://imkgo.github.io/img/bing/BluePondJapan_ZH-CN9068810300_1920x1080.jpg)

<!-- more -->

# 首先获取Bing背景图片的地址

访问 http://www.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1 可以获取Bing背景图片的信息，返回的数据格式如下
``` json
{
    "images": [
        {
            "startdate": "20160628",
            "fullstartdate": "201606281600",
            "enddate": "20160629",
            "url": "http://s.cn.bing.net/az/hprichbg/rb/KansasCropCircles_ZH-CN9416992875_1920x1080.jpg",
            "urlbase": "/az/hprichbg/rb/KansasCropCircles_ZH-CN9416992875",
            "copyright": "美国，在堪萨斯州西南部的时针式灌溉区 (© NASA/GSFC/METI/ERSDAC/JAROS, and U.S./Japan ASTER Science Team)",
            "copyrightlink": "http://www.bing.com/search?q=%E5%A0%AA%E8%90%A8%E6%96%AF%E5%B7%9E+%E6%97%B6%E9%92%88%E5%BC%8F%E5%96%B7%E7%81%8C&form=hpcapt&mkt=zh-cn",
            "wp": true,
            "hsh": "c4aac65e477f2f896034ad7d16fe190a",
            "drk": 1,
            "top": 1,
            "bot": 1,
            "hs": [],
            "msg": [
                {
                    "title": "今日图片故事",
                    "link": "http://www.bing.com/search?q=%E5%A0%AA%E8%90%A8%E6%96%AF%E5%B7%9E+%E6%97%B6%E9%92%88%E5%BC%8F%E5%96%B7%E7%81%8C&form=pgbar1&mkt=zh-cn",
                    "text": "堪萨斯州 时针式喷灌"
                }
            ]
        }
    ],
    "tooltips": {
        "loading": "正在加载...",
        "previous": "上一个图像",
        "next": "下一个图像",
        "walle": "此图片不能下载用作壁纸。",
        "walls": "下载今日美图。仅限用作桌面壁纸。"
    }
}
```
从图片信息中拿到图片文件的地址，把文件下载到本地。

# 使用win32gui设置桌面背景
``` Python
win32gui.SystemParametersInfo(win32con.SPI_SETDESKWALLPAPER, new_img_path, 1 + 2)
```
设置桌面背景的图片格式必须是.bmp格式，所以使用PIL将图片转换为.bmp格式后再设置桌面背景。完成这些之后程序就写完了。

# 定时执行Python程序
执行Python程序的方法写在一个VB脚本中，这样不会弹出一个命令框出来。
```
set a=createobject("wscript.shell")
a.run "cmd.exe /c python BingWallpaper.py" & Chr(34), 0
set a= Nothing

```
使用Windows自带的任务计划，每天去执行一遍VBS程序，这样就实现每天同步Bing的桌面壁纸了。


#### Python源码
``` Python
import requests
import json
import os
from PIL import Image
import win32gui
import win32con

bingUrl = "http://www.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1"
wallpaperDic = "F:/BingWallPapers/"

def download_img():
    r = requests.get(bingUrl)
    jobj = json.loads(r.text)
    images = jobj["images"]
    img_url = images[0]["url"]
    img_path = wallpaperDic + img_url[img_url.rindex("/") + 1:]
    if not os.path.exists(img_path):
        resp = requests.get(img_url)
        img_data = resp.content

        f = open(img_path, "wb")
        f.write(img_data)
        f.close()
    return img_path


def update_wallpaper():
    img_path = download_img()
    im = Image.open(img_path)
    new_img_path = img_path[:img_path.rindex(".")] + ".bmp"
    if not os.path.exists(new_img_path):
        im.save(new_img_path)
    win32gui.SystemParametersInfo(win32con.SPI_SETDESKWALLPAPER, new_img_path, 1 + 2)
    # os.remove(new_img_path)

update_wallpaper()

```
备注：Python版本 Python 3.5.1，依赖pywin32库，不同的Python版本对应的pywin32的库也不一样，需要下载适合你的Python版本（下载地址：https://sourceforge.net/projects/pywin32/files%2Fpywin32/）。
