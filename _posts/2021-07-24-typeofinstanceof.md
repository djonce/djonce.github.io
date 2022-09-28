---
layout: post
title: "typeof和instanceof的区别"
description: ""
category: [es6]
tags: [javascript]
---

{% include JB/setup %}

## 前言

虽然 typeof 和 instanceof 是 JavaScript 中的基础知识，但这个知识点却有很多的小细节。

## 数据类型

js 中有 7 种数据类型，其中有 6 种基本数据类型：

1、**undefined**

2、**Null**

3、**Boolean**

4、**Number**

5、**String**

6、**Symbol**
(在 es10 中加入了第 7 种原始类型 **BigInt**，现已被最新 Chrome 支持)
其中 **Symbol** 是 **ES6** 新增的，还有一种复杂类型（引用类型）Object。
我们常用 **typeof** 和 **instanceof** 操作符来判断数据类型

## typeof

**typeof**  是一个一元操作符不是函数，所以不需要传递参数，使用方法非常简单：**typeof A**，使用 typeof 会直接返回类型结果

```
// Numbers
typeof 37 === 'number';
// Strings
typeof "" === 'string';
// Booleans
typeof true === 'boolean';
// Symbols
typeof Symbol('foo') === 'symbol';
// Undefined
typeof undefined === 'undefined';
typeof blabla === 'undefined'; // 一个未定义的变量,或者一个定义了却未赋初值的变量
// Objects
typeof {a:1} === 'object';
type [1,2,3] = 'object'

// 函数
typeof function(){} === 'function';
// Null
typeof null === 'object'
```

我们可以轻松的用 **typeof** 判断数据类型，但是虚线下面的判断结构好像有点怪。这就涉及到了 typeof 实现原理的问题

### typeof 实现原理

让我们先来聊聊 **typeof** 的实现原理吧
js 在底层存储变量的时候，会在变量的机器码的低位 1-3 位存储其类型信息

- 000：对象
- 010：浮点数
- 100：字符串
- 110：布尔
- 1：整数

但是 **undefined** 和 **null** 这两个值却比较特殊

1、**null**：所有机器码均为 **0**

2、**undefined**：用 **−2^30** 整数来表示

这里我们先不管 **undefined**，现在你们知道为什么 **typeof** 判断 **null** 是对象了吗，因为 **null** 的机器码全是 0，所以被当成了 **object**。这个 bug 存在了将近 20 多年，也许永远也不会修复，因为这牵涉到太多的 web 系统，修复它会产生更多的 bug，令许多系统无法正常工作。

**typeof** 判断函数返回结果就是函数，这其实还好理解，函数也是对象，因为函数比较重要，有自己特殊的属性，所以我们用 **typeof** 判断函数时会区分与对象，单独显示。

但是万一我们要判断这个实例属于哪个对象，那显然这时 **typeof** 已经失去作用了，因为除了函数，它都会判断为对象，这时我们就需要用到 **instanceof** 了

## instanceof

**instance** 中文翻译为实例，因此 **instanceof** 的含义就不言而喻，判断该对象是谁的实例，同时我们也就知道 **instanceof** 是对象运算符。

### instanceof 实现原理

**instanceof** 的判断就是根据原型链进行搜寻，在对象 objA 的原型链上如果存在另一个对象 objB 的**原型属性**，那么表达式（**objA instanceof objB**）返回值为 true；否则返回 false。

下面代码说明了 instanceof 的使用方法

```
function Parent(){};
function Child(){};
function Other(){};

Child.prototype = new Parent();
let child = new Child();

child instanceof Child; // true
child instanceof Parent; // true
child instanceof Object; // true
child instanceof Other; // false

```

### 手写 instanceof

手写 **instanceof** 代码的实现原理也是面试的考察点，**instanceof** 核心实现如下，我们自己封装了一个具有 **instanceof** 功能的函数。

```
function instance_of(left, right) {
  let leftVal = left.__proto__;
  let rightVal = right.prototype;
  while (true) {
   if (leftVal === null) return false;
   if (leftVal === rightVal) return true;
   leftVal = leftVal.__proto__;
  }
}

let a = { title: 'hello world' }
console.log(instance_of(a, Object)); //true

```

## 总结

**typeof** 与 **instanceof** 都是判断数据类型的方法，区别如下：

1、**typeof** 会返回一个变量的基本类型，**instanceof** 返回的是一个布尔值

2、**instanceof** 可以准确地判断复杂引用数据类型，但是不能正确判断基础数据类型

3、而 **typeof** 也存在弊端，它虽然可以判断基础数据类型（null 除外），但是引用数据类型中，除了 function 类型以外，其他的也无法判断

参考链接：https://juejin.cn/post/7042164762456621087
