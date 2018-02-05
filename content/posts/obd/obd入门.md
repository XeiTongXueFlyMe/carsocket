---
title: "Obd入门"
date: 2018-02-01T09:41:45+08:00
draft: false
keywords: [ "OBD"]
categories: "OBD"
tags: [ "OBD" ]

# you can close something for this content if you open it in config.toml.
comment: true
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---

# OBD 产生背景

    从20世纪80年代起，美、同、欧等地的汽车制造企业开始在其生产的电喷汽车上配备车载自诊断模块(On—Board Diagnostics Module)。
    自诊断模块能在汽车运行过程中实时监测电控系统及其电路元件的工作状况，如有异常，根据特定的算法判断出具体的故障，并以诊断故障代码(DTC，Diagnostic Trouble Codes)的形式存储在汽车电脑芯片内阳1。系统自诊断后得到的有用信息可以为车辆的维修和保养提供帮助，维修人员可以利用汽车原厂专用仪器读取故障码，从而可以对故障进行快速定位，故障排除后，采用专用仪器清除故障码
    由于该时期不同厂。商的OBD系统之问各行其是、互不兼容，所以被称为第一代车载自诊断系统(OBD—I，the First On—Board Diagnostics)。为了统一标准，美国汽车工程师协会(SAE，Society of Automotive Engineers)1988年制定了OBD-II标准。OBD—II实行标准的检测程序，并且具有严格的排放针对性，用于实时监测汽车尾气排放情况。

# OBD的工作原理
    汽车在正常运行时，汽车的电子控制系统输入和输出的信号（电压或电流）会在一定的范围内有一定规律地变化；当电子控制系统电路的信号出现异常且超出了正常的变化范围，并且这一异常现象在一定时间（3个连续行程）内不会消失，ECU则判断为这一部分出现故障，故障显示灯点亮，同时监测器把这一故障以代码的形式存入内部RAM（Random Access Memory: 随机存储器），被存储的故障代码在检修时可以通过故障显示灯或OBDⅡ扫描仪来读取。如果故障不再存在，监控器在连续3次未接收到相关信号后，将指令故障显示灯熄灭。故障显示灯熄灭后，发动机暖机循环约40次，则故障代码会自动从存储器中被清除掉。

# OBD使用的通信协议
## OBD—II:
    1.ISO 9141—2
    2.ISO 14230—4(KWP2000)
    3.SAE J1850PWM
    4.SAE J1850 VPM
    5.ISO 15765-4 (CAN-BUS)

# OBD数据连接口
## 根据ISO DIS 15031–3中相关内容，DLC是一个如下16针的插座

![OBD插座图](http://res.cloudinary.com/xeitongxueflyme/image/upload/v1517623820/OBDjiekoutu_cmew5c.png)

## 各个针脚定义如下：

![OBD插座图](http://res.cloudinary.com/xeitongxueflyme/image/upload/v1517623808/OBDJIEKOUMIAOshu_egld52.png)

> 1, 3, 8, 9, 11, 12 和13 未做分配，可由车辆制造厂定义。

> 2, 6, 7, 10, 14 和 15 使用作诊断通讯的。根据实际使用的通讯协议的不同，它们往往不会都被使用，为使用的可由车辆制造厂定义。
