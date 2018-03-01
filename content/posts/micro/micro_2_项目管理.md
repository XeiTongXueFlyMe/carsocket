---
title: "Micro_2_项目管理"
date: 2018-02-27T10:03:06+08:00
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

------------------------

* 由于嵌入式设计，通常都有集成好的IDE，比如说使用STM32系列芯片，可以用KEIL，IAR等交叉编译IDE,而且这些工具都有编辑，编译，调试功能，那我们还需要其他的编辑工具不？答案是肯定的，一个好的工具能给程序员带来很好的习惯，带来不一样的灵感。

![progress](http://res.cloudinary.com/dynhsbwph/image/upload/v1519713279/progress.jpg)

------------------------

## 一个好的项目管理必须要有好的工具。下面将来说说工具。

1. 编译工具
2. 编辑工具
3. 代码版本管理工具
4. 团队协作工具

## 来谈谈编译工具。

我们通常采用的开发软件（keil,IAR,DAVE等），集成了编译，编辑功能。那我们怎么去选取编译器呢，一般用厂家推存的，如果有足够的经验，做一个编译工具也不是难事（有兴趣的，可以百度GCC工具链制作与使用）。

## 来谈谈编辑工具。

* 一个有经验有洁癖的程序员，都有一个爱不释手的编辑工具，什么是编辑工具，windos的文本编辑器和vim，就是典型的编辑器。当然我们采用IDE是集成编辑功能的，编写代码这些工具都可以，但是代码的重构，专用的编辑器却体现了自己的独到之处。

* 被大家公认的，一段好的代码，写起了过后，还需要不断的重构。然而重构，一个好的工具能帮程序员省不少时间。

* 如果只让我推存一个编辑工具，我会推荐大家使用 Visual Studio Code。当然大家都有自己喜欢的编辑工具。至少我认为Visual Studio Code能让我提高N倍的开发效率，不在使用鼠标。后面也会在代码质量章节，展示Visual Studio Code的代码重构的强大。

------------

* 放一张图，来看看Vscode的轻便性，Vscode轻量但不精简，具体查看官网的简绍。一个值得使用的编辑器。无论你采用什么平台（window，还是Linux），开发什么语言，VScode都值得你拥有。

![vscode](http://res.cloudinary.com/dynhsbwph/image/upload/v1519713288/vscode.png)

------------

## 来谈谈代码版本管理工具

### 毋庸置疑，首选git

 ------------

 ![git](http://res.cloudinary.com/dynhsbwph/image/upload/v1519714055/git.jpg)
 
 不要问为什么，全世界都在用的版本管理，如果刚接触git，建议学习廖雪峰老师的git入门教程，https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

 ------------

### git作为强大的分布式代码管理工具，其的衍生产品也有很多。

* 世界的大佬们总是喜欢站在巨人的肩上办事，于是出现了，git-gui,git-bash(window平台集成的命令行工具，集成了SSH等)，gitlab,github,gogs，TortoiseGit等超级好用的工具。

* 后面将用一个章节专门讲，怎么搭建局域网内部git服务器（标准的公司会选择私有GIT服务器），才入门的，建议学习和使用github来做代码的版本管理。现在许多公司，把有github账号并为大型项目做出贡献，作为面试的加分项。

* 不了解git的，建议百度了解一下git，这里就不阐述git的强大之处。

#### Micro选择会选择怎样的git版本管理框架？

* Micro从翻译来讲，微，可以理解为轻量级。为了大家入门尽可能简单些，但是功能又不能精简。于是Micro采用如下的git版本管理框架。公司团队强大的可以考虑使用gitlab。

1. git
2. TortoiseGit
3. gogs

> 后面会专门说怎么使用这样的git版本管理框架，和这样的框架有什么好处？给团队带来了哪些方便的功能呢？


## 来谈谈团队协作工具

### doxygen是不错的技术文档编写工具。

> 产品的开发，总会给我们带来奇妙的问题，特别是团队的协作，到底该不该写技术文档，无论是一个人开发，还是10多个人开发，其实技术文档是必不可少的，让人头痛的是，软件的版本更新周期快，然而技术文档的编写得跟上才行，怎么办呢？不必苦恼，我们遇到的问题，计算机前辈也会遇，他们的早帮我们解决了这些问题，推荐一个API编写的工具。

* 工具的下载地址为：http://www.stack.nl/~dimitri/doxygen/index.html

* 用 doxygen 制作的API文档示例：
https://xeitongxueflyme.github.io/j1939doc.github.io/

* 建议大家多看看doxygen的使用博客，看看大家是怎么用的。

### Beyond Compare是不错的代码比较器。

![beyang](http://res.cloudinary.com/dynhsbwph/image/upload/v1519894446/beyang.png)

### 文本格式转换器

* 经常我们阅读他人的代码时，我们的编辑器打开备注总是乱码。

> 这个问题的原因，可能是编辑器保存的文本格式不同，建议使用UTF-8的格式。

#### NotePad++ 是一个轻量级，不错的代码编辑器，有着强大的格式转换功能。所以可以当作格式转换器使用。

![notepad](http://res.cloudinary.com/dynhsbwph/image/upload/v1519895036/notepad.png)
