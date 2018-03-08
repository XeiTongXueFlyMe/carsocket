---
title: "Micro_3_代码质量"
date: 2018-03-08T18:13:00+08:00
draft: false
keywords: [ "Micro","嵌入式设计架构"]
categories: "micro"
tags: [ "Micro" ]

# you can close something for this content if you open it in config.toml.
comment: true
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---

# Micro代码质量

* 对于一个程序员来说，谈起怎么写出高质量的代码，这个问题是很令人兴奋的，一千个读者就要一千个哈姆雷特。我们不妨来简单的总结一下，这一千个哈姆雷特。

1. 高质量代码的产生，不是一次性写出的，需要不断的代码重构。（这也许是真理）
2. 能写出高质量代码的人，应该是有着极强代码重构能力的人。
3. 高质量代码的要求，代码规整，美观，易读，易扩展，易维护，易测试等，有些朋友会说还有，代码要独立化，设计要模块化，这些后面会说，并不属于代码质量，那是关于工程架构问题。
4. 对于团队，合作的写出一套高质量代码，是一件难事，首先就是各自都有自己的风格，那些没有风格的，简直是代码中的毒瘤，有些人喜欢linux风格，window风格，大小驼峰命名法，函数式开发流，分层次开发流，模块式开发流。驱动式开发等等。一个工程里，出现多种风格，是多么让人头痛，代码规整，美观，易读等都会严重下降。

## micro框架如何管理高质量代码

* micro框架认为 代码质量只与代码被重构的难易程度有关。

### 粗略的总结一下micro框架对代码质量把控提出的要求，

1. 允许代码中存在多风格，但是模块之间的接口统一风格，通过工程架构来解决这里带来的易读性，易维护性变差（微模块架构，软件设计架构章节会详细的说）。
2. 代码必须具有重构性。
3. 代码必须具有可测试性。这里需要了解测试金字塔。
4. 代码中不能存在全局变量的使用，这是一个迫害软件健康的流行感冒，种类多，好治理，但是难根治。长久的拖延下去，软件会出现意想不到的麻烦。
5. 每个函数的编写能一屏看完，严禁一个函数成千上万行。
6. 函数一般只干一件事。
7. 函数,一定要名副其实，不能挂羊头卖狗肉。
8. 先写测试函数，在写应用服务。

### 通过一个简单的示例，来看看什么是重构性。

> 我们来写一个计算器。

	1. 首先我们接到客户（产品经理）的需求，要做一个正整形数加法（0 ～ 127内）。


```c

unsigned char add(unsigned char a,unsigned char b)
{
	unsigned char _rt = 0;
	_rt = a+b;
	return _rt;
}

```

	2. 功能完成，只是第一步，还需优化代码，于是我们来对代码重构

```c

unsigned char add(unsigned char a,unsigned char b)
{
	return a+b;
}

```

	3. 这就是重构，简单吧，简单，恐怕我们想多了，这只是一个简单的示例。拿你以前写的代码，找到那个比较复杂的函数，看看能不能重构，是不是完全不能重构。

### 如何去保证代码重构，对现有工程的迫害。

* 代码重构可能会影响我们整个工程（动一发而牵连全身），举个例子。


1. 上一段代码很好用，客户很满意，但是客户现在还需要实现正负数的加法

	
```c

char add（char a, char b)
{
	return a+b;
}

```

> 我们对代码重构如上，有经验的工程师会发现，这是一段错误的代码，客户第一个要求是0～127的加法，第二个要求才是正负数，我们来看看 add(127,127)会返回254吗？这个例子展示了代码重构带来的风险，可能不经意间迫害了工程，有些工程师会说，开发时我就会想好这些，当客户提出第一个要求时，我的代码如下：

```c

double add（double a, double b)
{
	return a+b;
}

```

> 这段代码看似很完美，这种设计模式是不好的，按需求写代码，才真理，第一个函数虽然用了char,但是满足客户需求，不要画蛇添足，我们想的越多，犯错的机会越多，代码的质量越差（重构性差）。

## Micro框架下的编程方式

* 如何去保证代码重构，对现有工程的迫害。通过下面步骤看看，Micro框架是怎么要求工程师编程的。首先我们接到客户的需求，要做一个正整形数加法（0 ～ 127内）。

```c

```
	1. 先编写函数原型，这里对函数命名一定要名副其实。

```c
unsigned char add(unsigned char a,unsigned char b)
{
	;
}
```

	2. 编写测试函数

```c

void TestAdd1()
{
	if(add(127,127) == 254)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 001");
	}
}
void TestAdd2()
{
	if(add(2,3) == 5)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 002");
	}
}

```

	3. 在main中调用测试函数，TestAdd1（），TestAdd2()。

```c
//代码会输出如下信息：
unitTest: begin
err:	add.c code 001
err:	add.c code 002
unitTest: end
```

	4. 开始写add函数，因为代码输出错误信息了，说明我们的add.c中出现错误

```c
unsigned char add(unsigned char a,unsigned char b)
{
	return a+b;
}
```

	5. 再次调用第3步，
```c
//代码会输出如下信息：
unitTest: begin
..
unitTest: end
```
	6. 代码通过可以交付给客户。
	7. 上一个代码很好用，客户很满意，但是客户现在还需要实现正负数的加法
	8. 新的需求来了，函数原型可以用add(),因为这个函数是做加法的（名副其实），那我们先写测试代码。
```c

void TestAdd3()
{
	if(add(-2,3) == 1)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 003");
	}
}

```

	9. 重构函数add();

```c

char add（char a, char b)
{
	return a+b;
}

```

	10. 在main中调用测试函数，TestAdd1（），TestAdd2(),TestAdd3()对代码进行测试。

```c
//代码会输出如下信息：
unitTest: begin
err:	add.c code 001..
unitTest: end
```

> 这时，我们就可以发现为了实现新的需求，我们对代码就行了重构，迫害工程，但是往往这种错误，不能立即体现，我们高高兴兴的交付了代码，后面带来的只会是更多的产品维护时间成本。
> 按照Micro框架设计产品，我们在重构产品，重构代码时，就会发现这个隐匿式错误，我们会发现代码输出add.c code 001 ，说明测试不通过，我们还得继续修改代码。

	11. 发现错误，继续修改代码。

```c

short (short a, short b)
{
	return a+b;
}

```

	12. 在main中调用测试函数，TestAdd1（），TestAdd2(),TestAdd2()对代码进行测试，好交付。

```c
//代码会输出如下信息：
unitTest: begin
...
unitTest: end
```
	13. 交付代码后，我们就可以安稳的睡大觉了。对自己的代码也十分的放心。

#### 上面不仅讲代码重构，也讲了怎么去写一套可以测试的代码，让机器帮我们测试代码。Micro框架对此终结如下：

1. 别人用一天的时间开发一个服务，我们也许会用3天，测试代码是功能代码量的2倍左右，
2. 别人测试也许用一个月，我们也许就几秒钟。
3. 代码测试，虽然不是万能的，但是可以帮我们规避极大多数隐逸式问题，和所有调用逻辑问题，隐逸式问题多数发生在产品联调阶段，用户使用阶段。难调式，难解决，问题跟踪难。就拿上面的问题说，我客户要好久才会输入 127 + 127，这个我们不知道，当出现错误了，会不会是输入窗口不能翻译127 + 127呢，这个我们不也知道，会不会是我们的ADD()服务错了。一个小的错误，排查几天是常事。修改代码后，出现其他错误也是常事。


#### printf()函数是怎么来的

* 在嵌入式中没有printf()函数，打印信息很不方便。

##### 怎么让单片机支持printf()，我采用的是串口，当然也可以用SPI,CAN,485等，代码如下：

```c

#ifndef GB27930_DEBUG_EN
#define GB27930_DEBUG_EN
#endif

#ifdef GB27930_DEBUG_EN
#define printf(format, ...)  {char fifo[60]={0};sprintf(fifo,format, ## __VA_ARGS__);osPrintf(fifo);}
#else
#define printf(format, ...)
#endif

/**利用串口，打印字符串
*
* @param[in] str 字符串
* @note   本函数封装XMC4800的UART库函数
*/
void osPrintf(const char str[])
{
	int i;
	for(i=0;i<strlen(str);i++)
	{
		UART_TransmitWord(&UART_0,str[i]);

		while((UART_GetTXFIFOStatus(&UART_0) & XMC_USIC_CH_TXFIFO_EVENT_STANDARD) == 0);
		UART_ClearTXFIFOStatus(&UART_0, XMC_USIC_CH_TXFIFO_EVENT_STANDARD);
	}

}

```

## Micro框架是怎么嵌入测试代码的

* Micro框架认为软件开发，从代码角度来看，有测试模式，产品模式。通过一个宏来切换。

```c

#define UNITTESTEN  1/**< unitTestEn*/

```

### 通过一个简单的示例，来看看怎么嵌入测试代码。

* main.c

```c
/**
* @file main.c
*
* @author XeiTongXueFlyMe
*
* @date  2018.3.8
*
*/
#include  "includes.h"

int main(void)
{
	//你的代码，可能是初始化代码，因为测试代码可能会用串口等资源

#if UNITTESTEN
	unitTest();
#endif
	
	//你的代码
}

#if UNITTESTEN
void unitTest()
{
	printf("\nunitTest: begin");
	printf("\n");

	TestAdd1();
	TestAdd2();
	TestAdd3();

	printf("\nunitTest: end");
}
#endif
```

* include.h

```c
/**
* @file include.h
*
* @brief 统领头文件
*
* @author XeiTongXueFlyMe
*
* @date  2018.3.8
*
*/
#ifndef __INCLUDES_H
#define __INCLUDES_H

/*lib*/
#include <DAVE.h>
#include  "stdio.h"

/*module*/
#include "./module/printf.h"
#include "./module/add.h"

/*APP*/
#include "./UserApp/osLed.h"

#define UNITTESTEN  1/**< unitTestEn*/

#if UNITTESTEN
extern void unitTest();
#endif

#endif /* __INCLUDES_H */
```

* add.c

```c
/**
* @file add.c
*
* @author XeiTongXueFlyMe
*
* @date  2018.3.8
*
*/
#include  "includes.h"
/**加法
* @param[in] a 加数
* @param[in] b 加数
* @return  a+b
*
*/
short (short a, short b)
{
	return a+b;
}

#if UNITTESTEN

void TestAdd1()
{
	if(add(127,127) == 254)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 001");
	}
}
void TestAdd2()
{
	if(add(2,3) == 5)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 002");
	}
}
void TestAdd3()
{
	if(add(-2,3) == 1)
	{
		printf(".");
	}else
	{
		printf("\n err:	add.c code 003");
	}
}
#endif


```

* add.h

```c
/**
* @file add.h
*
* @author XeiTongXueFlyMe
*
* @date  2018.3.8
*
*/
#ifndef __ADD_H
#define __ADD_H

/**加法
* @param[in] a 加数
* @param[in] b 加数
* @return  a+b
*
*/
extern short (short a, short b);

#if UNITTESTEN
extern void TestAdd1();
extern void TestAdd2();
extern void TestAdd3();
#endif


#endif
```


#### Micro框架嵌入测试代码

* 当代码处于编写，调试，修改，修复BUG,联调测试时处于测试模式，

> include.h

```c

#define UNITTESTEN  1/**< unitTestEn*/

```

* 当产品处于交付，上量生产，代码处于产品模式

> include.h

```c

#define UNITTESTEN  0/**< unitTestEn*/

```


# 本章总结

我们用最简单的方式，描述两个大问题，代码重构，代码可测试化开发，需要实际动手，不断的吸取经验，才能把这两个用的很好。Micro提倡的这两种编程方法，希望给大家带来一种感受，那就是让产品开发，中期管理，后期维护，都变得如此简单。
