---
layout: post
title: URDF语法详解
date: 2023-08-01 10:00:00
description: URDF语法详解
tags: ROS
categories: ROS
---

# URDF语法详解

#### 1.robot标签

urdf 中为了保证 xml 语法的完整性，使用了`robot`标签作为根标签，所有的 link 和 joint 以及其他标签都必须包含在 robot 标签内,在该标签内可以通过 name 属性设置机器人模型的名称

**属性**

name: 指定机器人模型的名称

#### 2.link

urdf 中为了保证 xml 语法的完整性，使用了`robot`标签作为根标签，所有的 link 和 joint 以及其他标签都必须包含在 robot 标签内,在该标签内可以通过 name 属性设置机器人模型的名称

**属性**

name: 指定机器人模型的名称

**子标签**

-   visual ---> 描述外观(对应的数据是可视的)
    -   geometry 设置连杆的形状
        -   标签1: box(盒状)
            -   属性:size=长(x) 宽(y) 高(z)
        -   标签2: cylinder(圆柱)
            -   属性:radius=半径 length=高度
        -   标签3: sphere(球体)
            -   属性:radius=半径
        -   标签4: mesh(为连杆添加皮肤)
            -   属性: filename=资源路径(格式:**package://\<packagename>/\<path>/文件**)
    -   origin 设置偏移量与倾斜弧度
        -   属性1: xyz=x偏移 y便宜 z偏移
        -   属性2: rpy=x翻滚 y俯仰 z偏航 (单位是弧度)
    -   metrial 设置材料属性(颜色)
        -   属性: name
        -   标签: color
            -   属性: rgba=红绿蓝权重值与透明度 (每个权重值以及透明度取值\[0,1])
-   collision ---> 连杆的碰撞属性
-   Inertial ---> 连杆的惯性矩阵

在此，只演示`visual`使用。

例：

```c++
    <link name="base_link">
        <visual>
            <!-- 形状 -->
            <geometry>
                <!-- 长方体的长宽高 -->
                <!-- <box size="0.5 0.3 0.1" /> -->
                <!-- 圆柱，半径和长度 -->
                <!-- <cylinder radius="0.5" length="0.1" /> -->
                <!-- 球体，半径-->
                <!-- <sphere radius="0.3" /> -->

            </geometry>
            <!-- xyz坐标 rpy翻滚俯仰与偏航角度(3.14=180度 1.57=90度) -->
            <origin xyz="0 0 0" rpy="0 0 0" />
            <!-- 颜色: r=red g=green b=blue a=alpha -->
            <material name="black">
                <color rgba="0.7 0.5 0 0.5" />
            </material>
        </visual>
    </link>

```

#### 3.joint
