---
layout:     post
title:      Blog准备篇
subtitle:   了解MarkDown语法
date:       2018-11-22
author:     Rone
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - MarkDown
    - Blog
---

# 前言
>前人种树后人乘凉，在浏览[利用 GitHub Pages 快速搭建个人博客]这篇博客之后，开始了我的blog搭建之旅
>这篇blog旨在了解Blog的MarkDown的用法，非常感谢[邱柏荧](http://qiubaiying.top/about/)提供的如此详细的内容。
>
>[利用 GitHub Pages 快速搭建个人博客](https://www.jianshu.com/p/e68fba58f75c)

#### Title
- 使用 一对"---"的符号，用于区分Title的内容区域块
- 使用"subtitle: ***" 来定义博客的主题
- 使用"date:" 2016-10-18 来定义博客的时间
- 使用"tags:" 定义标签  “- MarkDown”用于表示对应标签内容
- 使用"header-img: img/***.jpg"定义Title中的背景,img/***.jpg是在git工程中的相对路径


#### 代码块
使用一对"```"将内容包起来
```
this my blog
this my blog
this my blog
```

#### 设置字体层级大小

1.一级字体 使用 # + “内容”的书写方式
# 一级字体

2.二级字体 使用 ## + “内容”的书写方式
## 二级字体

3.三级字体 使用 ### + “内容”的书写方式
### 三级字体

4.四级字体 使用 #### + “内容”的书写方式
#### 四级字体

5.五级字体 使用 ##### + “内容”的书写方式，这个比较特殊，它会同时设置成5级字体和灰色的效果
##### 五级字体

#### 设置字体
1.红色：`红色` A B A 的书写方式，A == "`"

2.灰色：*灰色* ，A B A 的书写方式，A == "`"

2.黑色：**黑色**，A B A 的书写方式，A == "`"

4.黑色斜体：/黑色斜体/，A B A 的书写方式，A == "`"

#### 嵌入图片
使用 ！【】（图片的地址）  符号使用英文版本，因为英文版本显示不出，所以使用中文符号做标识。
示范
![](http://ww4.sinaimg.cn/large/65e4f1e6gw1f8rti38wlxj20ke0d3n0h.jpg)

#### 设置超链接
通过"【1111】（www）"来定义超链接  1111 是显示内容 www 是链接 符号使用英文版本，因为英文版本显示不出，所以使用中文符号做标识。
例：拿google做示范 [Goodgle一下](https://www.google.com.hk)

Tips：不同语义块之间都需要空置出1-2个空格