---
layout: page
title: 爬虫知识记录
description: 从入门到进阶的爬虫学习笔记，包括 requests、BeautifulSoup、Selenium 等工具的使用
img:
importance: 2
category: work
---

## 前言

爬虫本质上是一种工具，基于学习 Python 和下载所需资源的目的，我开始了我的爬虫学习之旅。大一的时候开始学习爬虫，当时想着能爬点图片，然后就一发不可收拾，爬 konachan、然后是爬家教试卷、然后是网易云评论然后到 12306，但是一直没有总结，现在写个简单的总结过一下吧。

## 1. 爬虫初步

爬虫主要是基于 Python 中的 requests + BeautifulSoup 包实现，刚开始入门时，主要学习如何分割获取想要的信息。

### 正则匹配

```python
import requests
import re
import csv

f = open('试水.csv', 'w', encoding='gbk')
domain = "https://www.zxzjtv.com/"

resp = requests.get(domain, verify=False)
resp.encoding = 'utf-8'
obj1 = re.compile(r'<a href="(?P<url>.*?)" title="(?P<title>.*?)">', re.S)
obj2 = re.compile(r'<h1 class="title">(?P<title>.*?)</h1>.*?主演：(?P<actor>.*?)</p><p class="data">导演：(?P<daoyan>.*?)</p><p')

res = obj1.finditer(resp.text)
csv_writer = csv.writer(f)
csv_writer.writerow(["电影名", "演员", "导演"])

for a in res:
    url1 = domain + a.group('url')
    b = requests.get(url1, verify=False)
    c = obj2.finditer(b.text)
    for d in c:
        csv_writer.writerow([d.group('title'), d.group('actor'), d.group('daoyan')])
f.close()
```

### XPath

```python
import requests
from lxml import etree
import time

zhangshu = int(3401)

for yeshu in range(31, 666):
    url = f'https://gelbooru.wjcodes.com/index.php?tag=rating:questionable&p={yeshu}'
    resp = requests.get(url)
    print(f"第{yeshu}页--------------------------------------------------------------------------------------")
    yeshu += 1

    html = etree.HTML(resp.text)
    li = html.xpath('//*[@id="main-list"]/li')
    for lis in li:
        aa = lis.xpath('./a/button[2]/@onclick')
        urls = aa[0][15:aa[0].find(",") - 1]
        name = 'p' + aa[0][aa[0].find(",") + 2:aa[0].find(")") - 1] + '.' + urls.split(".")[-1]
        print(f"第{zhangshu}张", end=" ")
        huoqu = requests.session()
        img = huoqu.get(urls)
        print(name)
        name = f'F:\konachan china 15\\{name}'
        with open(name, mode="wb") as f:
            f.write(img.content)
        f.close()
        print("完成")
        zhangshu += 1
        time.sleep(1)
```

上述是我主用的两种匹配方式，也是我 Python 的启蒙。

## 2. 爬虫偷懒式 - Selenium

有些网站会做反爬虫，使用 requests + BeautifulSoup 的方式经常会被发现，然后就要加 header 啥的，使用 Selenium 工具可以直接模拟本人操作浏览器，只是速度会慢一些，但是应对反爬虫非常好用。

```python
from selenium.webdriver import Edge
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from chaojiying import Chaojiying_Client
import time

web = Edge()
usrname = '???'
password = '???'

def main():
    web.get("http://ehall.szu.edu.cn/new/index.html")
    time.sleep(2)

    el = web.find_element(By.XPATH, '//*[@id="ampHasNoLogin"]')
    el.click()
    time.sleep(2)

    web.find_element(By.XPATH, '//*[@id="username"]').send_keys(usrname)
    web.find_element(By.XPATH, '//*[@id="password"]').send_keys(password, Keys.ENTER)
    time.sleep(2)
    # ... 更多操作
```

上边是我写的抢体育场地的 Selenium 爬虫，简单易懂。

## 3. 反反爬虫手段

### 1. 加 Headers

```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36',
    'Connection': 'keep-alive',
    'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
}
```

### 2. 加代理

```python
proxies = {
    'http': 'http://127.0.0.1:7890/',
    'https': 'http://127.0.0.1:7890/'
}
```

### 3. 超级鹰识别验证码

```python
def yzm():
    chaojiying = Chaojiying_Client('用户名', '密码', '软件ID')
    im = open('验证码图片路径', 'rb').read()
    a = chaojiying.PostPic(im, 9104)
    print(a['pic_str'])
```

## 4. 快速爬虫手段

### 1. 进程池与线程池

一个进程里可以有多个线程，所以爬虫可以用多个进程或者多个线程或者多个进程同时加多个线程。但是实操爬虫的时候，爬取过快有些图片会损坏，线程池放多或者进程池放多都会损坏，损坏率随着爬取数量增加而升高。

### 2. 协程

将文件读写操作写入协程也可以加快爬虫速度，理论上协程也可以加上线程池进程池，但是考虑到实际爬取的数据丢失，可能线程池或者进程池去下载数据加上协程去写文件会更加快捷稳定。

## 5. 爬虫代码实例

以下代码基本是我手写，随着网页改变有些可能也用不了了，但是基本思想都在里面。

### 爬取微信公众号中的试卷图片

```python
import requests
from bs4 import BeautifulSoup
import re
import time

i = int(1)
url = 'https://mp.weixin.qq.com/s?__biz=...'
resp = requests.get(url)
resp.encoding = 'utf-8'
main_page = BeautifulSoup(resp.text, "html.parser")
alist = main_page.find_all("img", class_="rich_pages")

for b in alist:
    a = b.get("data-src")
    print(a)
    name = f"八上英语第一次月考{i}.jpeg"
    name = r'C:\Users\Acer\Desktop\八上英语第一次月考\\' + name
    i += 1
    print(i)
    sesstion = requests.session()
    img_res = sesstion.get(a)
    with open(name, mode="wb") as f:
        f.write(img_res.content)
    f.close()
    time.sleep(1)
print("all all over!")
```

## 总结

通过爬虫学习，我掌握了：

- 正则表达式和 XPath 的使用
- requests 和 BeautifulSoup 库
- Selenium 自动化工具
- 反爬虫应对策略
- 多线程、多进程和协程的使用
- 各种实际场景的爬虫实现

这些知识不仅帮助我完成了各种数据采集任务，也让我对网络协议、HTTP 请求等有了更深入的理解。
