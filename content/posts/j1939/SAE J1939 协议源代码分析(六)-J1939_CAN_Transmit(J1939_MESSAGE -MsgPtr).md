---
title: "J1939socket_v1.1.0源代码分析3"
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
|J1939_CAN_Transmit（）|与CAN驱动的接口|将和ChangeGroupIDofLMO（）联合移植（可不这样设计）先将传入函数的MsgPtr中的数据写到CAN的结构体（加载），调用CAN驱动的发送函数|

# 函数功能

   基于SAE J1939协议，将发送消息MsgPtr加载到CAN驱动自带的结构体中，然后发送调用CAN驱动的发送函数，发送消息。

# 函数参数

> 配合移植函数ChangeGroupIDofLMO（），确定传入ChangeGroupIDofLMO（）参数。

```
void J1939_CAN_Transmit(J1939_MESSAGE *MsgPtr)
{
	/*加载29位ID*/
	ChangeGroupIDofLMO(can驱动的结构体，MsgPtr);
	/*加载数据长度*/
	;
	/*加载数据*/
	;
	/*加载RTR*/
	;
	/*开始发送数据，调用CAN驱动的发送函数*/
	;
}
```
J1939_MESSAGE *MsgPtr 为要传入的参数，J1939_MESSAGE 消息结构体在J1939.h中，按J1939的收发格式做的。
```
#define J1939_MSG_LENGTH	5  //消息长度
#define J1939_DATA_LENGTH	8  //数据长度
union J1939_MESSAGE_UNION 
{ 
	struct   me 
	{ 
		unsigned int	DataPage			: 1; 
		unsigned int	Res					: 1; 
		unsigned int	Priority			: 3; 
		unsigned int	PDUFormat_Top		: 3;
		unsigned char	PDUFormat;					
		unsigned char	PDUSpecific; 
		unsigned char	SourceAddress; 
		unsigned int	DataLength 			: 4; 
		unsigned int	RTR					: 4;	
		unsigned char	Data[J1939_DATA_LENGTH]; 
	};
	struct me Mxe;
	unsigned char		Array[J1939_MSG_LENGTH + J1939_DATA_LENGTH]; 
}; 
```
传入ChangeGroupIDofLMO（）参数根据你的设计方案。
> 这里我们默认方法，先配置结构体，再将结构体，写入CAN控制器（如果你写的CAN驱动，也支持这样的拓扑）。

# 移植示例
> 采用英飞凌XMC4500,对can驱动层，提取了CAN结构体，CAN_NODE3_DEBUG.lmobj_ptr[1]。将J1939协议自带的结构体 J1939_MESSAGE 中的数据写入CAN的结构体中。

**我们默认移植方案是：**
1. CAN驱动存在结构体（有些喜欢用很多全局变量），将与CAN有关的数据统一。
2. CAN驱动方式为，先写驱动结构体，在调用相应的函数将结构体中的数据配置CAN。

```
/*
*输入： J1939_MESSAGE *MsgPtr ，协议要发送的消息，
*输出： 
*说明：从MsgPtr加载到CAN驱动自带的结构体中
		将和ChangeGroupIDofLMO（）联合移植（可不这样设计）
		先将传入函数的MsgPtr中的数据写到CAN的结构体（加载），调用CAN驱动的发送函数
*/
void J1939_CAN_Transmit(J1939_MESSAGE *MsgPtr)
{
	CAN_NODE_LMO_t *lmo_ptr = CAN_NODE3_DEBUG.lmobj_ptr[1];
	/*加载29位ID*/
	ChangeGroupIDofLMO((const CAN_NODE_LMO_t * const)(CAN_NODE3_DEBUG.lmobj_ptr[1]),MsgPtr);
	/*加载数据长度*/
	lmo_ptr->mo_ptr->can_data_length = MsgPtr->Mxe.DataLength;
	CAN_NODE_MO_Init(lmo_ptr);
	/*加载数据*/
	lmo_ptr->mo_ptr->can_data_byte[0] = MsgPtr->Mxe.Data[0];
	lmo_ptr->mo_ptr->can_data_byte[1] = MsgPtr->Mxe.Data[1];
	lmo_ptr->mo_ptr->can_data_byte[2] = MsgPtr->Mxe.Data[2];
	lmo_ptr->mo_ptr->can_data_byte[3] = MsgPtr->Mxe.Data[3];
	lmo_ptr->mo_ptr->can_data_byte[4] = MsgPtr->Mxe.Data[4];
	lmo_ptr->mo_ptr->can_data_byte[5] = MsgPtr->Mxe.Data[5];
	lmo_ptr->mo_ptr->can_data_byte[6] = MsgPtr->Mxe.Data[6];
	lmo_ptr->mo_ptr->can_data_byte[7] = MsgPtr->Mxe.Data[7];
	/*加载RTR*/
	//我的CAN驱动中默认是0，1939-21协议要求也为零，所以不在配置
	/*开始发送数据*/
	(CAN_NODE_STATUS_t) CAN_NODE_MO_Transmit(lmo_ptr) ;
}
```
# 程序说明
  J1939_CAN_Transmit(J1939_MESSAGE *MsgPtr)与ChangeGroupIDofLMO（）配合移植，设计者根据自己的需求，可将两者融合，博主之所以分开，是为了以后做网络管理器。和故障诊断工程预留。