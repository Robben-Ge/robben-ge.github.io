---
layout: post
title: rqt常用插件
date: 2023-08-01 10:00:00
description: rqt常用插件
tags: ROS
categories: ROS
---

# rqt常用插件

#### 1.rqt\_graph

**简介:** 可视化显示计算图

**启动:** 可以在 rqt 的 plugins 中添加，或者使用`rqt_graph`启动

#### 2.rqt\_console

**简介:** rqt\_console 是 ROS 中用于显示和过滤日志的图形化插件

**准备:** 编写 Node 节点输出各个级别的日志信息

```c++
/*  
    ROS 节点:输出各种级别的日志信息

*/
#include "ros/ros.h"

int main(int argc, char *argv[])
{
    ros::init(argc,argv,"log_demo");
    ros::NodeHandle nh;

    ros::Rate r(0.3);
    while (ros::ok())
    {
        ROS_DEBUG("Debug message d");
        ROS_INFO("Info message oooooooooooooo");
        ROS_WARN("Warn message wwwww");
        ROS_ERROR("Erroe message EEEEEEEEEEEEEEEEEEEE");
        ROS_FATAL("Fatal message FFFFFFFFFFFFFFFFFFFFFFFFFFFFF");
        r.sleep();
    }


    return 0;
}

```

**启动:**

可以在 rqt 的 plugins 中添加，或者使用`rqt_console`启动

#### 3.rqt\_plot

**简介:** 图形绘制插件，可以以 2D 绘图的方式绘制发布在 topic 上的数据

**准备:** 启动 turtlesim 乌龟节点与键盘控制节点，通过 rqt\_plot 获取乌龟位姿

**启动:** 可以在 rqt 的 plugins 中添加，或者使用`rqt_plot`启动

#### 4.rqt\_bag

**简介:** 录制和重放 bag 文件的图形化插件

**准备:** 启动 turtlesim 乌龟节点与键盘控制节点

**启动:** 可以在 rqt 的 plugins 中添加，或者使用`rqt_bag`启动
