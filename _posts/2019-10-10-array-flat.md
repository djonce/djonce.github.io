---
layout: post
title: "数组拍平"
description: ""
category:
tags: []
---

{% include JB/setup %}

## 什么是数组拍平

数组拍平方法 Array.prototype.flat() 也叫数组扁平化、数组拉平、数组降维。
指的是把多维数组变成一维数组。

## 数组拍平的使用场景

复杂场景下的数据处理 （例如：echarts 大屏数据展示）

## 常用实现数组拍平 3 种方法

```
const array = [1,3,4,[4,5,12,67],  [7,8,[6,90,5]]];
```

### 使用 reduce 函数

代码如下

```js
function flatten(array) {
  return array.reduce((prev, current) => {
    return prev.concat(Array.isArray(current) ? flatten(current) : current);
  }, []);
}
```

### es6 flat 函数

```js
function flatten(array) {
  return array.flat(Infinity);
}
```

### while 循环扩展运算符

```js
function flatten(array) {
  while (array.some(Array.isArray)) {
    array = [].concat(...array);
  }
  return array;
}
```
