---
layout: page
title: 微信机器人
description: 基于 Docker 的微信机器人项目，实现群聊自动化管理、关键词回复、ChatGPT 问答等功能
img:
importance: 3
category: work
---

## 项目简介

微信机器人项目是我在加入信安 Aurora 后，发现群里有个机器人，但微信网页版已经无法使用，因此开始研究如何实现微信机器人。通过这个项目，我学习了 Python 面向对象编程、Linux 基础、服务器运维和 Docker 等相关知识。

## 技术实现

### 1. Windows 版本

最初使用的是 Windows 版本，但每次都需要在本地电脑上运行，还需要降级微信版本，使用几天后觉得太麻烦就放弃了。

### 2. Docker 版本

后来找到了一个 wechat-bot 的 Docker 项目，购买服务器后部署了这个 Docker 容器。通过特定端口访问服务器登录微信，然后启动 hook 程序即可。主要的开发工作集中在 hook 消息后的功能开发。

## 实现功能

### 1. 进群欢迎

处理邀请与扫描入群的不同情况。

```python
from wesdk import *
if __name__ == "__main__":
    bot = Bot()
    bot.register("on_open", lambda ws: logging("Connecting to WeChat service .."))
    bot.register("on_close", lambda ws: logging("Byebye~"))
    bot.register("join_room", lambda msg: (
        bot.send_msg('\n٩(๑^o^๑)۶\n欢迎新来的小老虎！！！\n这里是深大福建同乡会\n请将群备注修改为:\n年级【研/本】-姓名-专业-地区\n如需帮助请说:\n小福！help！help！',
                     msg['content']['id1'], msg['content']['id1'],
                     msg['content']['content'].split('"')[-2])
        if('邀请' in msg['content']['content'])
        else bot.send_msg('\n٩(๑^o^๑)۶\n欢迎新来的小老虎！！！\n这里是深大福建同乡会\n请将群备注修改为:\n年级【研/本】-姓名-专业-地区\n如需帮助请说:\n小福！help！help！',
                         msg['content']['id1'], msg['content']['id1'],
                         msg['content']['content'].split('"')[1])
        if('扫描' in msg['content']['content'])
        else None
    ), print(msg))

    bot.run()
```

### 2. 关键词回复

支持自定义关键词回复功能，可以动态添加和删除关键词。

```python
key_replys = {
    "你好": "你好",
    "晚安": "晚安",
    "早安": "早安",
    "早上好": "早上好",
}

def addkey(keyword):
    if(keyword == '.k -a'):
        content = ''
        for i in key_replys:
            content = content + f'{i}:{key_replys[i]}\n'
    else:
        keywords = keyword.split(" ", 2)
        if(len(keywords) != 3):
            content = '输入格式错误,请重新输入'
        else:
            key_replys[keywords[1]] = keywords[2]
            content = '添加匹配规则成功'
    return content
```

### 3. ChatGPT 问答

集成 ChatGPT 功能，支持智能问答。

```python
def get_chatgpt(keyword):
    if(keyword.startswith('小福小福')):
        keyword = keyword[5:]
    elif(keyword.startswith('.chat')):
        keyword = keyword[6:]
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect(("服务器地址", 1234))
        data = keyword
        s.sendall(data.encode())
        data1 = s.recv(1024)
        data1 = data1.decode()
        data1 = data1[1:]
        return(data1)
```

### 4. 天气查询

支持查询指定地区的天气信息。

```python
def get_weather(keyword):
    if(keyword == '.w'):
        keyword = "广东省深圳市南山区"
    else:
        keyword = keyword[3:]
    url = f'https://www.msn.cn/zh-cn/weather/forecast/in-{keyword}'
    resp = requests.get(url)
    resp.encoding = 'utf-8'
    html = etree.HTML(resp.text)
    # 解析天气信息...
    return content
```

### 5. 每日运势

随机生成每日运势，增加趣味性。

```python
def get_ys():
    list_1 = ['$大凶$', '$中凶$', '$凶$', '$小凶$', '$小吉$', '$吉$', '$中吉$', '$大吉$']
    weight = [1, 2, 3, 4, 4, 3, 2, 1]
    # 根据运势生成宜忌内容...
    return content
```

### 6. 功能锁

支持动态开启和关闭特定功能。

```python
functionlock = {
    '.w': 1,
    '.l': 0,
    '.k': 1,
    '.dk': 1,
    '.chat': 1,
    '小福小福 ': 1
}
```

## 项目总结

通过这个项目，我深入学习了：

- Python 面向对象编程
- Linux 系统基础操作
- Docker 容器化部署
- WebSocket 通信
- 服务器运维

这个项目不仅解决了实际需求，也让我在技术实践中获得了宝贵的经验。
