---
layout: post
title: ROS节点名称重名
date: 2023-08-01 10:00:00
description: ROS节点名称重名
tags: ROS
categories: ROS
---

# ROS节点名称重名

1.rosrun设置命名空间

```python
rosrun turtlesim turtlesim_node __ns:=/xxx

rosrun turtlesim turtlesim_node __ns:=/yyy

```

结果：

```python
/xxx/turtlesim
/yyy/turtlesim

```

2.rosrun名称重映射：

```python
rosrun turtlesim  turtlesim_node __name:=t1 |  rosrun turtlesim   turtlesim_node /turtlesim:=t1(不适用于python)
rosrun turtlesim  turtlesim_node __name:=t2 |  rosrun turtlesim   turtlesim_node /turtlesim:=t2(不适用于python)

```

结果：

```python
/t1
/t2

```

3.rosrun名称重映射与设置命名空间叠加：

```python
rosrun turtlesim turtlesim_node __ns:=/xxx __name:=tn

```

4.launch文件设置命名空间与重映射：

```python
<launch>

    <node pkg="turtlesim" type="turtlesim_node" name="t1" />
    <node pkg="turtlesim" type="turtlesim_node" name="t2" />
    <node pkg="turtlesim" type="turtlesim_node" name="t1" ns="hello"/>

</launch>

```

结果：

```python
/t1
/t2
/hello/t1

```

5.编码设置命名空间与重映射：

c++：

```c++
#名称别名
ros::init(argc,argv,"zhangsan",ros::init_options::AnonymousName);
#命名空间
std::map<std::string, std::string> map;
map["__ns"] = "xxxx";
ros::init(map,"wangqiang");

```

python：

```python
rospy.init_node("lisi",anonymous=True)
```
