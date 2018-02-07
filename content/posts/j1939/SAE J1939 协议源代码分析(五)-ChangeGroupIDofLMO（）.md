---
title: "J1939socket_v1.1.0源代码分析2"
date: 2018-01-31T15:16:36+08:00
draft: false
keywords: [ "j1939","SAE J1939"]
categories: "j1939"
tags: [ "j1939","j1939源代码"]

# you can close something for this content if you open it in config.toml.
comment: true
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---

# 预备知识

1. 熟悉CAN2.0B协议，及相关硬件驱动开发 
2. 熟悉SAE J1939协议

# 函数名

|函数名|类型|注解|
|:--:|:--:|:---:|
|ChangeGroupIDofLMO（）|功能函数|设置远程帧29位，在函数J1939_CAN_Transmit中被调用|

# 函数功能

   基于SAE J1939协议，我们需要配置can的远程帧ID,函数ChangeGroupIDofLMO（）提供这样的配置。配置方法和移植，和J1939_CAN_Transmit函数有关，建议一起配置：
   
# 函数参数

> 配合移植函数J1939_CAN_Transmit（），确定传入参数。

```
void ChangeGroupIDofLMO(const CAN_NODE_LMO_t *lmo_ptr,J1939_MESSAGE *MsgPtr)
{
	//你的移植代码;
}
```
传入参数根据，你的设计方案，这里我们默认方法，先配置结构体，再将结构体，写入CAN控制器。

# 移植示例

> 采用英飞凌XMC4500,对can驱动层，提取了配置ID的结构体，lmo_ptr->mo_ptr->can_identifier 。将J1939协议自带的结构体 J1939_MESSAGE 中的ID写入CAN的结构体中。

```
void ChangeGroupIDofLMO(const CAN_NODE_LMO_t *lmo_ptr,J1939_MESSAGE *MsgPtr)
{
	int _i=0;
	lmo_ptr->mo_ptr->can_identifier = 0;
	/*读取J1939协议结构体中的数据到 CAN的结构中*/
	for(_i=0;_i<4;_i++)
	{
		lmo_ptr->mo_ptr->can_identifier = (lmo_ptr->mo_ptr->can_identifier << 8) + MsgPtr->Array[_i];
	}
	//将can结构体中的配置，写入到CAN设备
    CAN_NODE_MO_Init(lmo_ptr);
}
```
# 程序说明

  本函数传入两个变量，第一个是CAN的ID配置结构体，可根据设计，不传入，直接写在代码中，第二个传入的参数是J1939自带的结构体，在程序中代表要发送的信息的结构体。本函数只提取结构体中的ID信息。

	