---
title: "J1939socket_v1.1.0源代码分析4"
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
2. 熟悉SAE J1939协议http://blog.csdn.net/xietongxueflyme/article/details/74276702/
# 函数名
|函数名|类型|注解|
|:--:|:--:|:---:|
|J1939_CAN_Receive（）|与CAN驱动的接口|将设备CAN中的数据取出，存入J1939_MESSAGE结构体中，如果没有数据，返回0|

# 函数功能

   基于SAE J1939协议，读取CAN驱动的数据，如果没有数据，返回0，否则将设备CAN中的数据取出，存入J1939_MESSAGE结构体中，返回1.

# 函数参数
> 读取CAN驱动的数据，如果没有数据，返回0, 否则返回1.**函数必须返回**

```
/*
*输入：J1939_MESSAGE *MsgPtr 数据要存入的内存的指针
*输出： 
*说明：读取CAN驱动的数据，如果没有数据，返回0
		将设备CAN中的数据取出，存入J1939_MESSAGE结构体中
*/
int J1939_CAN_Receive(J1939_MESSAGE *MsgPtr)
{
	if ( ) //判断有没有新的数据
	{
	  // 清除接受标识位
	  ;
	  // 读取数据
	  ;
	  //将29位标志位（can_identifier）写入J1939的结构中
	  _id = XXX; //假如_id等于读取CAN驱动中的ID
	  MsgPtr->Array[0] = _id>>(8*3);
	  MsgPtr->Array[1] = _id>>(8*2);
	  MsgPtr->Array[2] = _id>>(8*1);
	  MsgPtr->Array[3] = _id>>(8*0);
	  //读取数据长度
	  MsgPtr->Mxe.DataLength = XXXXXXX;
	  if (MsgPtr->Mxe.DataLength > 8)
		  MsgPtr->Mxe.DataLength = 8;
	  //读取数据
	  MsgPtr->Mxe.Data[0] = XXXXXX;
	  MsgPtr->Mxe.Data[1] = XXXXXX;
	  MsgPtr->Mxe.Data[2] = XXXXXX;
	  MsgPtr->Mxe.Data[3] = XXXXXX;
	  MsgPtr->Mxe.Data[4] = XXXXXX;
	  MsgPtr->Mxe.Data[5] = XXXXXX;
	  MsgPtr->Mxe.Data[6] = XXXXXX;
	  MsgPtr->Mxe.Data[7] = XXXXXX;

	  return 1;
	}else
	{
		return 0;//没有消息
	}
}
```
J1939_MESSAGE *MsgPtr 数据要存入的内存的指针，J1939_MESSAGE 消息结构体在J1939.h中，按J1939的收发格式做的。
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
判断是否有数据接受，有就存下数据返回1，没有返回0，**函数必须返回**，判断函数你的CAN驱动得支持。
> **清除接受标识位**这个步根据设计可要可不要，举个例子，我在XMC4500上移植，当有新的消息来时，标识位置位，可读取数据，但是不清除标识位，再次进入J1939_CAN_Receive(J1939_MESSAGE *MsgPtr)时，判断函数认为又有新消息（实际没有），读取数据后返回1.所以，**我的移植必须要清除标识位**（用来判断有没有数据）。

# 移植示例
> 采用英飞凌XMC4500,对can驱动层，提取了CAN结构体，CAN_NODE3_DEBUG.lmobj_ptr[0]。将J1939协议自带的结构体 J1939_MESSAGE 中的数据写入CAN的结构体中。

```
/*
*输入：J1939_MESSAGE *MsgPtr 数据要存入的内存的指针
*输出： 
*说明：读取CAN驱动的数据，如果没有数据，返回0
		将设备CAN中的数据取出，存入J1939_MESSAGE结构体中
*/
int J1939_CAN_Receive(J1939_MESSAGE *MsgPtr)
{
	uint32_t receive_status=0;
	uint32_t _id=0;
	receive_status = CAN_NODE_MO_GetStatus( ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]);
	if ( receive_status & XMC_CAN_MO_STATUS_RX_PENDING)  //XMC_CAN_MO_STATUS_NEW_DATA
	{
	  // 清除接受标识位
	  CAN_NODE_MO_ClearStatus(((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0],XMC_CAN_MO_RESET_STATUS_RX_PENDING);
	  // 读取数据
	  CAN_NODE_MO_Receive( ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]);
	  //将29位标志位（can_identifier）写入J1939的结构中
	  _id = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_identifier;
	  MsgPtr->Array[0] = _id>>(8*3);
	  MsgPtr->Array[1] = _id>>(8*2);
	  MsgPtr->Array[2] = _id>>(8*1);
	  MsgPtr->Array[3] = _id>>(8*0);
	  //读取数据长度
	  MsgPtr->Mxe.DataLength = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_length;
	  if (MsgPtr->Mxe.DataLength > 8)
		  MsgPtr->Mxe.DataLength = 8;
	  //读取数据
	  MsgPtr->Mxe.Data[0] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[0];
	  MsgPtr->Mxe.Data[1] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[1];
	  MsgPtr->Mxe.Data[2] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[2];
	  MsgPtr->Mxe.Data[3] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[3];
	  MsgPtr->Mxe.Data[4] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[4];
	  MsgPtr->Mxe.Data[5] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[5];
	  MsgPtr->Mxe.Data[6] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[6];
	  MsgPtr->Mxe.Data[7] = ((CAN_NODE_t *)&CAN_NODE3_DEBUG)->lmobj_ptr[0]->mo_ptr->can_data_byte[7];

	  return 1;
	}else
	{
	  return 0;//没有消息
	}
}
```
# 程序说明
  J1939_CAN_Receive(J1939_MESSAGE *MsgPtr)函数**函数必须存在返回**。