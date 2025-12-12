---
layout: page
title: 智能仓储竞赛项目
description: 基于大疆 RoboMaster EP 底盘和 Mavic 3T 无人机的智能仓储系统，实现车机协同、二维码识别等功能
img:
importance: 4
category: work
---

## 项目简介

智能仓储算是第一个我主导的比赛，也算是我花费最大精力从头跟到尾的比赛，我了解比赛的所有流程和所有大致技术。比完赛没有马上去总结，结果拖着拖着就过了半年，可能有些技术细节模糊了，但是至少在比赛中，我确实感到学习了不少东西。

虽然前期为了获得设备搞了线上赛，整理了项目规划等细节，但主要记录的就是智能车和无人机开发的学习过程。车是大疆 RoboMaster 的 EP 底盘，改装，无人机是大疆的 Mavic 3T，行业应用版的无人机精度挺高的。

## 智能车开发

### 1. 底盘控制

比赛使用的是大疆 RoboMaster 的 EP 底盘，这个底盘整体上开发很成熟，阅读说明书熟悉一下就可以操控，通过 WiFi 模块将其接入车载算力，通过 ROS 控制。如果没记错这个底盘大疆配有对应的控制的 ROS 包，导入车载算力盒子编译然后建立通信即可用 ROS 控制。

### 2. 智能车改装

原始底盘没有办法停无人机，给底盘上方加装亚克力板，亚克力板上贴二维码辅助无人机降落。

### 3. 车载算力盒子配置

天途提供的车载算力盒子算是还行，只跟着教程烧录了几遍 Ubuntu 18.04 系统，然后配置 ROS。

### 4. 二维码识别

尝试过两三种主流的 Python 识别二维码的工具包，zbar、zxing 等，最后使用 pyzbar 直接进行识别。

```python
#!/usr/bin/python3
# coding:utf8

import cv2
import pyzbar.pyzbar as pyzbar
import numpy as np
import pandas as pd
from sensor_msgs.msg import Image
import rospy

class QRCode_Dect:
    def __init__(self):
        # load parameters
        image_topic = rospy.get_param('~image_topic', '/ep_cam/image_raw')
        self.color_image = Image()
        self.getImageStatus = False

        # image subscribe
        self.color_sub = rospy.Subscriber(image_topic, Image, self.image_callback,
                                          queue_size=1, buff_size=52428800)

        # if no image messages
        while not rospy.is_shutdown():
            while (not self.getImageStatus):
                rospy.loginfo("waiting for image.")
                rospy.sleep(2)

    def image_callback(self, image):
        self.getImageStatus = True
        self.color_image = np.frombuffer(image.data, dtype=np.uint8).reshape(
            image.height, image.width, -1)
        self.gray = cv2.cvtColor(self.color_image, cv2.COLOR_BGR2GRAY)

        im, rects_list, polygon_points_list, QR_info = self.decodeDisplay(self.gray)

        # 把检测到二维码的信息再绘制到BGR彩色图像上
        for data in zip(rects_list, polygon_points_list, QR_info):
            x, y, w, h = data[0]
            polygon_points = data[1]
            text = data[2]
            self.write_csv(file_path, text)
            self.drop_duplicate(file_path)
            cv2.polylines(self.color_image, [polygon_points], isClosed=True,
                         color=(255, 0, 255), thickness=2, lineType=cv2.LINE_AA)
            cv2.putText(self.color_image, text, (x, y - 10),
                       cv2.FONT_HERSHEY_SIMPLEX, .5, (0, 0, 125), 2)

        cv2.imshow("grey_img", im)
        cv2.imshow("color_img", self.color_image)
        cv2.waitKey(1)

    def decodeDisplay(self, image):
        barcodes = pyzbar.decode(image)
        rects_list = []
        polygon_points_list = []
        QR_info = []

        for barcode in barcodes:
            (x, y, w, h) = barcode.rect
            rects_list.append((x, y, w, h))
            cv2.rectangle(image, (x, y), (x + w, y + h), (0, 0, 255), 2)
            polygon_points = barcode.polygon

            extract_polygon_points = np.zeros((4, 2), dtype=np.int32)
            for idx, points in enumerate(polygon_points):
                point_x, point_y = points
                extract_polygon_points[idx] = [point_x, point_y]

            extract_polygon_points = extract_polygon_points.reshape((-1, 1, 2))
            polygon_points_list.append(extract_polygon_points)

            barcodeData = barcode.data.decode("utf-8")
            barcodeType = barcode.type
            text = barcodeData
            QR_info.append(text)
            rospy.loginfo("Found {} barcode: {}".format(barcodeType, barcodeData))
        return image, rects_list, polygon_points_list, QR_info

def main():
    rospy.init_node('QRCode_Dect', anonymous=True)
    qrcode_dect = QRCode_Dect()
    rospy.spin()

if __name__ == "__main__":
    file_path = '/home/tta/ws_warehouse/src/warehouse/scripts/test.csv'
    main()
```

### 5. 获取无人机中的二维码

无人机搭配的算力盒子算力不足以支持识别二维码，在车载盒子上通过 SSH 通信把固定位置的无人机拍摄照片下载到车载盒子上识别。

```bash
#!/bin/bash

remote_folder="/mnt/img"
local_path="/home/gehaotian/Ros/img"
remote_host="root@192.168.1.114"
password="123456"

# 创建本地目标文件夹
mkdir -p "$local_path"

# 在SFTP会话中下载整个文件夹
sshpass -p "$password" sftp "$remote_host" << EOF
get -r "$remote_folder" "$local_path"
EOF

if [ $? -eq 0 ]; then
  echo "Folder download successful"
else
  echo "Folder download failed"
fi
```

### 6. 合并二维码识别结果

最后无人机识别结果也会通过 ROS 通信传到车载盒子上，编写 merge 程序进行合并去重。

```python
# 遍历文件夹，合并csv文件，去重写入新csv文件
import os
import csv

def merge_csv_files(folder_path, output_file):
    # 获取文件夹中的所有CSV文件
    csv_files = [filename for filename in os.listdir(folder_path)
                 if filename.endswith('.csv')]
    # 存储所有行数据的集合，用于去重
    unique_rows = set()

    for csv_file in csv_files:
        with open(os.path.join(folder_path, csv_file), 'r', newline='') as file:
            reader = csv.reader(file)
            for row in reader:
                # 将每一行数据转换为元组，并添加到集合中
                unique_rows.add(tuple(row))

    # 将去重后的行数据写入合并后的CSV文件
    with open(output_file, 'w', newline='') as file:
        writer = csv.writer(file)
        for row in unique_rows:
            writer.writerow(row)
```

## 无人机开发

这次比赛感觉除了烧录机载算力盒子的系统以外并不是说涉及很多底层的东西，无人机是大疆 Mavic 3 行业应用版，使用机载盒子去连接控制，实现车机协同。

无人机底盘基于 Mavic 3，接了个算力盒子，然后调算力调半天。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/smart-warehouse-drone.jpg" title="大疆 Mavic 3T 无人机" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    大疆 Mavic 3T 行业应用版无人机，用于智能仓储系统的车机协同
</div>

## 技术栈

- **ROS**: 机器人操作系统，用于底盘控制和通信
- **Python**: 二维码识别、数据处理
- **OpenCV**: 图像处理
- **pyzbar**: 二维码识别库
- **Ubuntu 18.04**: 车载算力盒子系统
- **大疆 RoboMaster EP**: 智能车底盘
- **大疆 Mavic 3T**: 行业应用版无人机

## 项目总结

通过这个项目，我深入学习了：

- ROS 机器人操作系统
- 车机协同系统设计
- 二维码识别技术
- 图像处理和计算机视觉
- 嵌入式系统开发
- 系统集成和调试

这是一个综合性的项目，涉及硬件、软件、算法等多个方面，让我对机器人系统有了更全面的理解。
