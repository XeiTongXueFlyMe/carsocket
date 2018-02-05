---
title: "Obd快速入门"
date: 2018-02-03T11:25:02+08:00
draft: false
keywords: [ "obd","obd快速入门"]
categories: "obd"
tags: [ "obd" ]

# you can close something for this content if you open it in config.toml.
comment: true
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---
# 简介：
OBD的概念起源于美国加州空气资源管理委员会(CARB) ，目的是为了降低和控制汽车尾气对大气的污染。OBD的相关技术标准主要是由国际标准化组织(ISO)和国际汽车工程师协会(SAE)制定的。这些标准已经被普遍接受，并且仍在不断发展和更新之中。 

# OBD 的功能：
1. 记录失效或故障发生时的运行工况条件
2. 触发故障警示灯（MI）
3. 配备标准的诊断仪接口
4. 采用标准方法读取ECU中的数据
5. 监测与排放有关的零部件和子系统
6. 监测三元催化器
7. 监测氧传感器
8. 监测油箱通风系统
9. 监测二次空气系统
10. 监测废气再循环系统
11. 失火监测

# 世面上的OBD版本

--------------------------------------------------

## 第一代OBD（OBD-I）

1. 加州环保局（CARB）1985年立法，1988年开始实施
2. 诊断要求针对硬件失效
3. 主要零部件包括氧传感器，废气再循环阀，供油系统和发动机控制系统
4. 没有统一的故障码和通讯协议标准

-------------------------------------------------

## 第二代OBD（OBD-II）
1. 加州环保局于1989年立法，针对1994-96年及以后生产的车型
2. 扩大了诊断零部件范围
3. 增加了对系统的诊断要求，如催化器失效，失火，蒸汽泄漏等
4. 以对排放的影响为主，导入失效的具体排放条件
5. OBD-II排放限值随LEV，ULEV，SULEV等排放标准不同
6. 建立了标准化故障码和通讯协议标准

-------------------------------------------------

## 联邦OBD（Federal OBD-II）
1. 适用于加州以外的49州
2. 要求类似加州OBD-II

-------------------------------------------------

## 欧洲OBD（EOBD）
1. 欧3法规实施时同步实施（2000年）
2. 法规要求类似美国OBD-II
3. 没有EVAP泄漏测试要求
4. 欧4对EOBD的要求和欧3相同
5. 欧5实施时的EOBD要求：
    * EOBD排放限值更严格
    * 增加对三元催化器NOx转换率劣化的诊测

# OBD使用拓扑

![OBD使用拓扑图](http://res.cloudinary.com/xeitongxueflyme/image/upload/c_scale,w_507/v1517629220/OBD%E4%BD%BF%E7%94%A8%E6%8B%93%E6%89%91%E5%9B%BE_cd8vwb.png)
