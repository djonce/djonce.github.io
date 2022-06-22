---
layout: post
title: "CSS盒子模型"
description: "CSS盒子模型"
category: []
tags: [CSS]
---
{% include JB/setup %}


### 什么是盒模型
```
CSS规范的一个模块，定义了一个长方形的盒子，包括各自的content、padding、border、margin。常译为盒子模型、盒模型或框模型。
```

### 盒模型分类

* 从标准上分为**标准盒模型**和**替代（IE)盒模型**;
* 从元素类型上分为**块级盒子（block box)**和**内联盒子（Inline box)**;


### 两种盒模型的区别

* W3C标准盒子模型(content-box): **内容是盒子的边界**；
* W3C标准盒子模型(content-box): **width=内容宽度**；
* IE盒子模型（border-box): **边框才是盒子的边界**；
* IE盒子模型（border-box): **width=内容宽度 + padding + border**；

### 如何设置两种盒子模型

在不设置**box-sizing**的情况下，**box-sizing**默认是**coontent-box**.

```
/* 标准模型 */
box-sizing: content-box;

/* IE模型 */
box-sizing: border-box;
```

### 块级盒子和内联盒子

这两种盒子会在**页面流(page flow)** 和 元素之间的关系方面表现不同的行为：


#### 块级盒子

1、盒子可以占据父容器的所有可用空间；

2、每个盒子都会换行；

3、**width**和**height**属性可以发挥作用；

4、默认情况下，**h1-h6,p,div,section**都处于block状态；


#### 内联盒子

1、盒子不会产生换行；

2、**width**和**height**属性将不起作用；

3、默认情况下用做链接的**a**元素、**span**、**em**、以及**strong**都处于inline状态。

#### 特殊的行内块(inline-block)
 如果不希望**一个项切换到新行**，但希望可以**设定宽度和高度**，此时可以将元素为**inline-block**

#### 元素类型切换
- 块级盒子，内联盒子以及行内盒子主要通过**display属性**来设置对应的值分别为：
- 块级盒子： **display:block**
- 内联盒子： **display:inline**
- 行内块盒子： **display:inline-block**


### 盒子型属性设置

margin

- margin:10px 50px;  //上下10px 左右50px
- margin:10px 50px 20px; //上:10px 左右50px 下20px
- margin:10px 50px 20px 0px; //上10px 右50px 下20px 左0px;

padding属性:padding简写属性控制元素所有四条边的内边距区域。

> 该属性是以下属性的简写:padding-bottom,padding-left,padding-right,padding-top。

padding简写模式对应的值分别为
- padding:10px 50px; //上下10px 左右50px
- padding:10px 50px 20px; //上:10px 左右50px 下20px
- padding:10px 50px 20px 0px; //上10px 右50px 下20px 左0px;

border属性:CSS的border属性是一个用于设置各种单独的边界属性的简写属性。
> border可以用于设置一个或多个以下属性的值：
border-width,border-style,border-color。



例子:
- border:solid;  //chrome下默认3px实线边框
- border:dashed red;  //chrom下默认3px红色虚线边框
- border:1px solid;  //1px实线边框
- border:10px double red;  //10px双实线红色边


### 小结

1、什么是盒模型?

CSS基础框盒模型是CSS规范的一个模块,它定义了一种长方形的盒子。

2、盒模型的分类有？

盒模型从标准定义上分为标准盒模型和替代（IE）盒模型,从元素类型上分为块级盒子和内联盒子

3、盒模型的切换:

通过box-sizing属性进行切换：
ie盒模型:box-sizing:border-box;
标准盒模型:box-sizing:content-box;

4、盒模型属性设置，margin和padding值的设置:

1个值的情况:如10px4个方向都为10px；

2个值的情况:如10px20px上下10px左右20px

3个值的情况:如10px20px30px上10px左右20px下30px

4个值的情况:如10px20px30px40px上10px右20px下30px左40px(顺时针)

border值:border:1pxsolidred三个值分别代表border-width,border-style,border-color.可设置一个或多个属性的值