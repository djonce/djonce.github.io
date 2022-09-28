---
layout: post
title: "css box BFC"
description: "CSS block formatting context"
category: 
tags: [css]
---
{% include JB/setup %}


### 什么是BFC?

块格式化上下文（Block Formatting Context, BFC)是web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

它是 CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。

简单的说BFC是一个完全独立的空间，这个空间里子元素的渲染不会影响到外面的布局

### 触发BFC的条件

* **浮动元素**(float除了none以外的值)
* **定位元素**(position: absolute / fixed)
* **display**(值为inline-block/ table-cell/- table-caption/ flex/ inline-flex)
* **overflow**(值为hidden/atuo/srcoll)设置有这些属性的box，都会产生BFC


### BFC 特性

* 内部的盒子在垂直方向上一个接一个地放置
* 垂直方向上地距离由margin决定，在同一个BFC的box中，相邻的两个box边距会重叠
* BFC的区域不会与float box重叠
* 计算BFC的高度时，浮动元素也参与计算
* BFC就是一个独立的容器，里面的子元素不受外面的元素影响


### BFC的作用

#### 1.解决margin重叠问题（添加独立BFC)

看下面例子

未设置BFC之前

```css
 CSS代码
.child{
  width: 100px;
  height: 100px;
  background-color: orange;
  margin: 30px;
}
​
Html代码
<div class="child"></div>
<div class="child"></div>

```

设置BFC后


```
CSS代码
.child{
  width: 100px;
  height: 100px;
  background-color: orange;
  margin: 30px;
}
.bfc{
  overflow: hidden;
}
​
html代码
<div class="child"></div>
<div class="bfc">
    <div class="child"></div>
</div>

```

设置BFC后，第一个child的margin-bottom不会和第二个child的margin-top重叠，这也是BFC元素的另一个原则，不会影响到外边的box，是一个独立的区域。

效果图对比

![效果图](/images/posts/css/bfc-1.png)

#### 2.解决浮动高度塌陷问题（在父元素添加overflow：hidden）

当我们不给父元素设置高度，子元素设置了浮动的时候，会发生高度塌陷，代码和效果图如下。

![效果图](/images/posts/css/bfc-2.png)


```

CSS代码
.father {
    border: 5px solid rgb(91, 243, 30);
    width: 460px;
    
}
.child{
    width: 200px;
    height: 200px;
    border:5px solid orange;
    float: left;
}
​
html代码
<div class="father">
    <div class="child"></div>
    <div class="child"></div>
</div>

```

当我们在父元素的样式中加入overflow：hidden，就能解决float高度塌陷问题。

```

CSS代码
.father {
    border: 5px solid rgb(91, 243, 30);
    width: 460px;
    overflow: hidden;
}
.child{
    width: 200px;
    height: 200px;
    border:5px solid orange;
    float: left;
}
​
html代码
<div class="father">
    <div class="child"></div>
    <div class="child"></div>
</div>

```

效果图就会发生变化

![效果图](/images/posts/css/bfc-3.png)


引用： https://zhuanlan.zhihu.com/p/264250523


### 小结

讲清楚如下三点:

#### 1. 什么是 BFC?

**BFC 是 Block Formatting Context，块格式化上下文的缩写，简单的说 BFC 是一个完全独立的空间, 这个空间里子元素的渲染不会影响到外面的布局。**


#### 2. 如何创建 BFC?(可以触发生成 BFC 的条件有哪些?) 我们只需记住如下常见的几种就可以:

**display: table-cell  
display: flex  
display: inline-block  
overflow: hidden  
position: absolute  
position: fixed** 

#### 3. BFC解决的什么问题?

**1.垂直方向margin重叠的问题。  
2.使用Float脱离文档流，父元素高度塌陷问题。**