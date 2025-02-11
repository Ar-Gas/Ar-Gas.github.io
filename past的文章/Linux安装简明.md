---
title: Linux + Ubuntu安装简明
date: 2023-09-16 09:41:00
tags: [Linux]
categories: [学习笔记,Linux]
mathjax: true
---

因一段时间未用虚拟机，打开后发现崩了QAQ，故重新配置了一下，写个文档节约下次时间。

# 安装VMware17

bilibili找的盗版（雾

# 安装Ubuntu的ISO

在阿里云或者其他大学镜像安装对应，我下载的版本是：ubuntu-20.04.6-live-server-amd64

# 安装linux 配置

[参考链接](https://www.bilibili.com/video/BV1ig4y1s7eL/?vd_source=a326e65c6d0fa8e7b1904a3c161ec105)

<!--more-->

1. 选择创建新的虚拟机

   ![虚拟机](https://user-images.githubusercontent.com/111374823/268424253-c1c5218d-f2cf-4fbc-b7c4-a95741914546.png)

2. 选择稍后安装操作系统

   ![](https://user-images.githubusercontent.com/111374823/268424217-ffc1bbb4-68e8-4207-81df-a4b920d160f7.png)

3. 选择Linux ，版本Ubuntu 64bit

   ![](https://user-images.githubusercontent.com/111374823/268424216-52dc92e6-127a-4311-ac88-ff3894e7e925.png)

4. 选择安装位置

   ![](https://user-images.githubusercontent.com/111374823/268424215-59ec9b88-0ada-4621-8b05-dec41298640e.png)

5. 磁盘大小，自定义即可，我选的50GB

   ![](https://user-images.githubusercontent.com/111374823/268424213-9c0e5bcf-7909-411e-90fd-313cb2fdd969.png)

6. 虚拟内存（默认4GB）

7. 网络类型： NAT（使用物理机的网）

8. 处理器数量和内核不超过自身计算机即可

9. 其他默认

   ![](https://user-images.githubusercontent.com/111374823/268424208-484d9a46-6a02-4c47-afc2-db7bac44d5fd.png)

10. 在CD/DVD中设置ISO

    ![](https://user-images.githubusercontent.com/111374823/268424219-17e04947-0737-46f4-b97c-10b0c398ff32.png)

11. 然后打开虚拟机，依次安装[B站视频](https://www.bilibili.com/video/BV1ig4y1s7eL/?vd_source=a326e65c6d0fa8e7b1904a3c161ec105)的操作，安装图形界面需要的模块，进入图形模式设置中文。