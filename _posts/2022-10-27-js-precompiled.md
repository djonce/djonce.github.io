---
layout: post
title: "js预编译"
description: ""
category:
tags: [js]
---

{% include JB/setup %}

## 预编译

预编译简单理解就是，在代码执行前一刻发生的事情。预编译的过程，也就是 GO 和 AO 创建的过程。

### GO

**-> var -> function -> 执行**

> 创建 GO（Global Object）对象；

> 1、寻找变量声明作为 GO 的属性名，并赋值为 undefined；

> 2、寻找函数声明，放入作为 GO 的属性，并赋值为其函数体。

### AO

**var 和形参 -> 赋值 -> function -> 执行**

> 1、再函数被调用之前，创建 AO（Activation Object）对象，又叫执行期上下文；(上下文是唯一的，一个函数被多次被调用时会创建多个上下文)

> 2、寻找形式参数和变量声明作为 AO 的属性名，并赋值为 undefined；

> 3、传入实际参数的值；

> 4、在函数体内寻找函数声明，放入作为 AO 的属性，并赋值为其函数体。

### 实例

```
function test() {
  console.log(b);
  if (a) {
    var b = 100;
  }
  console.log(b);
  c =  234;
  console.log(c);
}

var a;
test();
a = 10;
console.log(c);


-----------
GO = {
  // 1、var  2、function 3、 执行；
  a: undefined,
  test: fn,
  c: 234
}

AO = {
  // 1、var和形参  2、赋值 3、function 4、执行；
  b: undefined
}

-> undefined
-> undefined
-> 234
-> 234

```
