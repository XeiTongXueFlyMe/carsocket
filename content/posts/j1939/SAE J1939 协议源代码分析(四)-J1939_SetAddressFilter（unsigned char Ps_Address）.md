---
title: "J1939socket_v1.1.0源代码分析1"
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
|J1939_SetAddressFilter（）|与CAN的移植接口|设置滤波器函数，起到接受滤波作用|

# 函数功能

   基于SAE J1939协议，我们需要CAN控制器提供至少3个滤波器给J1939协议代码。三个滤波器分别配置如下：
1. 设置滤波器0，只接受广播信息（PF = 240 -255）。
2. 设置设置滤波器1，2只接受全局地址（J1939_GLOBAL_ADDRESS）
3. 随着程序的运行，将改变滤波器2，来适应程序逻辑。

`J1939_SetAddressFilter()` 是用来设置滤波器2的，  函数主要设置PS位（目标地址），其目的是，让控制器只接受发送给本设备的消息。
    
# 函数参数

```
#define Port_SetAddressFilter(Address) J1939_SetAddressFilter(Address)

void J1939_SetAddressFilter(unsigned char Ps_Address)
{
	//你的移植代码;
}
```
传入参数 Ps_Address是我们要滤波的 PS值（0 - 255）。

# 移植示例
> 采用英飞凌XMC4500,对can驱动层，配置了3个滤波器，拿滤波器2来作为J1939_SetAddressFilter（） 设置的滤波器。

```
void J1939_SetAddressFilter(unsigned char Ps_Address)
{
	/*配置can驱动的结构体，设置滤波器2的PS字段*/
	CAN_NODE3_DEBUG.lmobj_ptr[2]->mo_ptr->can_id_mask &=0XFFFF00FF;
	CAN_NODE3_DEBUG.lmobj_ptr[2]->mo_ptr->can_id_mask |= (Ps_Address<<8);
	/*将can驱动结构体，重新写入滤波器2*/
	CAN_NODE_MO_Init(CAN_NODE3_DEBUG.lmobj_ptr[2]);
}
```

# 程序说明
	本函数提供给协议自身使用，除非协议被当做网络管理器。