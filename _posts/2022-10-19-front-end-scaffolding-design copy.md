---
layout: post
title: "前端架手架设计"
description: ""
category:
tags: [工程化]
---

{% include JB/setup %}

## 架构图

![效果图](https://19ba.oss-cn-hangzhou.aliyuncs.com/blog/img/WechatIMG345.jpeg)

## 脚手架的核心价值

自动化：项目重复拷贝/git 操作/发布上线操作

标准化：项目创建/ git flow/ 发布流程/ 回滚流程

数据化：研发过程系统化、数据化、使得研发过程可量化

## 开发流程

- 创建 npm 项目
- 创建脚手架入口文件，最上方添加：

```shell
#!/usr/bin/env node
```

- 配置 package.json，添加 bin 属性
- 编写脚手架代码
- 将脚手架发布到 npm

## 使用流程

- 安装脚手架
  pnpm install -g 91-cli

- 使用脚手架
  91-cli

## 脚手架开发难点

- 分包：将复杂的系统拆分成若干个模块
- 命令注册：

```shell
def create
def add
def publish
```

- 参数解析

```
def command [options] <params>
```

- 帮助文档
  global help
  Usage Options Commands

其它： 命令行交互、日志打印、命令行文字变色、网络通讯（http/websocket)、文件处理等

## 脚手架本地调试

主要解决本地库之间的依赖，引用问题

可以通过 npm link 来进行未发布上线的包依赖

大致步骤

- 链接本地脚手架

```
cd  your-cli-dir
npm link
```

- 链接本地库文件

```
  cd your-lib-dir
  npm link
  cd your-cli-dir
  npm link your-lib
```

- 取消链接本地库文件：

```
cd your-lib-dir
npm unlink
cd your-cli-dir
# link存在
npm unlink your-lib
# link不存在
rm -rf node_modules
npm install
```

理解**npm link**

- npm link your-lib: 将当前项目中\*\*node_modules 下指定文件链接到 node 全局 node_modules 下的库文件

- npm link : 将当前项目链接到 node 全局 node_modules 中作为一个库文件， 并解析 bin 配置创建可执行文件

理解 **npm unlink**

- npm unlink: 将当前项目从 node 全局 node_modules 中移除
- npm unlink your-lib： 将当前项目中的库文件依赖移除

## 脚手架注册命令和参数解析

const argv = require('process').argv;
console.log(argv);

## 源码地址

https://github.com/djonce/def-cli

更新中。。。
