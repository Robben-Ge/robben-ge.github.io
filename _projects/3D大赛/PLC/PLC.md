# PLC

#### 1.设备组态

控制器CPU：

![](image/image_bwKeO5sHQx.png)

IO扩展：

![](image/image_1S5-4UMxFd.png)

![](image/image_Tvt7Ye0EM9.png)

网络视图、硬件输入里面添加屏幕：

![](image/image_-vOpJ72GjV.png)

#### 2.相机通讯

添加通讯程序块：

![](image/image_2Pkqc2Rogn.png)

在通信开放式用户通信中：

![](image/image_lux3iAQ-fW.png)

添加之后组态

![](image/image__G1FbLOdHe.png)

伙伴地址为相机地址，端口定为9730或现场查看

添加相机数据块

![](image/image__REIcuXV4x.png)

![](image/image_kpMMh3zfF2.png)

回到程序点击相机发送拖入data

![](image/image_2SUYRATlXO.png)

接收数据块：

![](image/image_PKGWng-7dm.png)

#### 3.机器人通讯

![](image/image_ejLQGC9Sj4.png)

![](image/image_L9p9jkMBj2.png)

机器配置对照：

![](image/image_UvrZPb8rza.png)

添加机器人数据块

![](image/image_ScwL8Imefx.png)

R写到131

注意，数据编写完成后，点击属性，选择优化块访问。

还需要个系统通讯对照块(存IP应该是在机器人数据里建即可)

ID为相机往后推，addr为机器人ip地址，端口为机器人端口502一般

![](image/image_M7IB7YZ_OD.png)

添加到程序中：

![](image/image_1JqqURVOcw.png)

控制发送过程可以建立三个数据块：

![](image/image_rh6_uYFLae.png)

![](image/image_slVaSDV465.png)

![](image/image_YNzA2ja-kt.png)

如何开机启动：

选择firstscan，然后把启动0置S

设置DI、DO，起始与数量由机器决定，编译

![](image/image_EQZmX8vJiX.png)

第三个：

![](image/image_XzMi1DySuh.png)
