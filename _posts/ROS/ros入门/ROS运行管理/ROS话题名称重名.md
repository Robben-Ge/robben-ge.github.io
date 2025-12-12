---
layout: post
title: ROS话题名称重名
date: 2023-08-01 10:00:00
description: ROS话题名称重名
tags: ROS
categories: ROS
---

# ROS话题名称重名

1.rosrun设置话题重映射

```c++
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/turtle1/cmd_vel

rosrun turtlesim turtlesim_node /turtle1/cmd_vel:=/cmd_vel

```

2.launch文件设置话题重映射

```c++
<node pkg="xxx" type="xxx" name="xxx">
    <remap from="原话题" to="新话题" />
</node>

```

exa：

```c++
<launch>

    <node pkg="turtlesim" type="turtlesim_node" name="t1" />
    <node pkg="teleop_twist_keyboard" type="teleop_twist_keyboard.py" name="key">
        <remap from="/cmd_vel" to="/turtle1/cmd_vel" />
    </node>

</launch>

```

3.编码设置话题名称

c++

```c++
1）全局名称
  ros::Publisher pub = nh.advertise<std_msgs::String>("/chatter",1000);
  /chatter
2）相对名称
  ros::Publisher pub = nh.advertise<std_msgs::String>("chatter",1000);
  xxx/chatter
3）私有名称
  ros::NodeHandle nh("~");
  ros::Publisher pub = nh.advertise<std_msgs::String>("chatter",1000);
  /xxx/hello/chatter

```

python

```c++
1）全局名称
  pub = rospy.Publisher("/chatter",String,queue_size=1000)
  /chatter
2）相对名称
  pub = rospy.Publisher("chatter",String,queue_size=1000)
  xxx/chatter
3）pub = rospy.Publisher("~chatter",String,queue_size=1000)
  /xxx/hello/chatter

```
