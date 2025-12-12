---
layout: post
title: ROS分布式通信
date: 2023-08-01 10:00:00
description: ROS分布式通信
tags: ROS
categories: ROS
---

# ROS分布式通信

#### 1.准备

先要保证不同计算机处于同一网络中，最好分别设置固定IP，如果为虚拟机，需要将网络适配器改为桥接模式；

#### 2.配置文件修改

分别修改不同计算机的 /etc/hosts 文件，在该文件中加入对方的IP地址和计算机名:

主机端：

```c++
从机的IP    从机计算机名

```

从机端：

```c++
主机的IP    主机计算机名

```

设置完毕，可以通过 ping 命令测试网络通信是否正常。

```c++
IP地址查看名: ifconfig
计算机名称查看: hostname
```

#### 3.配置主机IP

\~/.bashrc追加

```c++
export ROS_MASTER_URI=http://主机IP:11311
export ROS_HOSTNAME=主机IP

```

#### 4.配置从机IP

配置从机的 IP 地址，从机可以有多台，每台都做如下设置:

\~/.bashrc 追加

```c++
export ROS_MASTER_URI=http://主机IP:11311
export ROS_HOSTNAME=从机IP

```

#### 测试

1.主机启动 roscore(必须)

2.主机启动订阅节点，从机启动发布节点，测试通信是否正常

3.反向测试，主机启动发布节点，从机启动订阅节点，测试通信是否正常
