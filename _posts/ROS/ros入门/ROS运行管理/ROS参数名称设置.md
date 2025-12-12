---
layout: post
title: ROS参数名称设置
date: 2023-08-01 10:00:00
description: ROS参数名称设置
tags: ROS
categories: ROS
---

# ROS参数名称设置

1.rosrun设置参数

```c++
rosrun turtlesim turtlesim_node _A:=100

```

2.launch文件设置参数

```c++
<launch>

    <param name="p1" value="100" />
    <node pkg="turtlesim" type="turtlesim_node" name="t1">
        <param name="p2" value="100" />
    </node>

</launch>

```

```c++
/p1
/t1/p1

```

3.编码设置参数

c++ ros::param

```c++
ros::param::set("/set_A",100); //全局,和命名空间以及节点名称无关
ros::param::set("set_B",100); //相对,参考命名空间
ros::param::set("~set_C",100); //私有,参考命名空间与节点名称

/set_A
/xxx/set_B
/xxx/yyy/set_C

```

c++ ros::NodeHandle

```c++
ros::NodeHandle nh;
nh.setParam("/nh_A",100); //全局,和命名空间以及节点名称无关
nh.setParam("nh_B",100); //相对,参考命名空间
ros::NodeHandle nh_private("~");
nh_private.setParam("nh_C",100);//私有,参考命名空间与节点名称

/nh_A
/xxx/nh_B
/xxx/yyy/nh_C
```

python

```c++
rospy.set_param("/py_A",100)  #全局,和命名空间以及节点名称无关
rospy.set_param("py_B",100)  #相对,参考命名空间
rospy.set_param("~py_C",100)  #私有,参考命名空间与节点名称

/py_A
/xxx/py_B
/xxx/yyy/py_C

```
