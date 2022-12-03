---
layout: post
title: "react源码包结构分析"
description: ""
category:
tags: [react]
---

{% include JB/setup %}

## React package 工程目录

> React 工程目录的 packages 下包含 **36** 个包（@17.0.2 版本）其中与 web 相关的核心包共有 **4** 个。本文以 4 个包为线索进行展开，深入分析 react 的架构原理。

包目录

```json
packages
├── create-subscription
├── dom-event-testing-library
├── eslint-plugin-react-hooks
├── jest-mock-scheduler
├── jest-react
├── react
├── react-cache
├── react-art
├── react-client
├── react-debug-tools
├── react-devtools
├── react-devtools-core
├── react-devtools-extensions
├── react-devtools-inline
├── react-devtools-scheduling-profiler
├── react-devtools-shared
├── react-devtools-shell
├── react-devtools-timeline
├── react-dom
├── react-fetch
├── react-interactions
├── react-is
├── react-native-renderer
├── react-noop-renderer
├── react-reconciler
├── react-refresh
├── react-server
├── react-server-dom-relay
├── react-server-dom-webpack
├── react-server-native-relay
├── react-test-renderer
├── react-transport-dom-relay
├── react-transport-dom-webpack
├── scheduler
├── shared
└── use-subscription
```

## 基础包结构

1、react

> react 基础包，只提供定义 react 组件（**ReactElement**) 的必要函数， 一般来说需要和渲染器（react-dom, react-native)一同使用， 在编写 **react** 引用代码时，大部部分调用此包的 api.

2、react-dom

> react 渲染器之一，是 react 与 web 平台链接的桥梁（可以在浏览器和 nodejs 环境中使用）,将 react-reconciler 中运行结果输出到 web 界面上，在编写 react 应用的代码时，大多数场景下，能用到 此包的 ReactDOM.render (< APP/>, document.getElementById('root')), 其余使用的 api,基本是 react 包提供。

3、react-reconciler

> react 得以运行的核心包 （协调 react-dom, react, scheduler 各个包之间的调用和配合).管理 react 应用状态的输入和结果的输出。将输入的信号转换成输出信号传递给渲染器。

- 接受输入 (scheduleUpdateOnFiber),将 fiber 树生成逻辑封装到一个回调函数中 （涉及 fiber 树形结构，fiber.updateQueue 队列，调和算法等）。
- 把此回调函数 （performSyncWorkOnRoot 或 performConcurrentWorkOnRoot) 送入 scheduler 进行调度
- **scheduler** 会控制回调函数执行时机， 回调函数执行完成后得到全新的 fiber 树
- 再调用渲染器 （如 react-dom, react-native 等）将 fiber 树形结构最终反映到界面上

4、scheduler

> 调度机制的核心实现， 控制由 **react-reconciler** 送入的回调函数的执行时机， 在 concurrent 模式下可以实现任务分片，在编写 **react** 应用代码时，
> 同样几乎不会直接调用到此包提供的 api.

- 核心任务就是执行回调 （回调函数由 **react-reconciler** 提供）
- 通过控制回调函数的执行时机，来达到任务分片的目的，实现可中断渲染 （concurrent 模式下才有此特性）

## 架构分层

为了便于理解， 可将 react 应用整体结构 分为 接口层（api) 和 内核层 （core) 2 个部分

### 1、接口层 **react**包

平时开发过程中使用到绝大部分 api 均在 react 包中。在 react 启动后，正常触发渲染的操作有**3**个。

- class 组件中使用 setState()
- function 组件里面使用 hook, 并发起 dispatchAction 去改变 hook 对象
- 改变 context

以上 setState 和 dispatchAction 都由 react 包直接暴露，所以基本调用 react 包 API 去与其它包进行交互

### 2、内核层 （core) 整个内核部分， 由 3 部分构成

- 调度器 scheduler 包，核心职责只有 1 个 ，就是执行回调  
  a、把 react-reconciler 提供的回调函数，包装到一个任务对象中。  
  b、在内部维护一个任务队列，优先级高的排在最前面。  
  c、循环消息任务队列，直到队列清空

- 协调器 react-reconciler 包，有 3 个核心职责：  
  a、装载渲染器，渲染器必须实现 HostConfig 协议（如：react-dom),保证在需要的时候，能够正确调用渲染器 api,生成实际节点（如：dom 节点）;
  b、接收 react-dom 包 （初次 render)和 react 包（后续更新 setState)发起的更新请求。
  c、将 fiber 树的构造过程包装在一个回调函数中，并将此回调函数传入到 scheduler 包等待调度。

- 渲染器 react-dom 包，有 2 个核心职责：
  a、引导 react 应用的启动 （通过 ReactDOM.render)
  b、实现 HostConfig 协议 （ReactDOMHostConfig.js 中）,能够将 react-reconciler 包构造出来 fiber 树表现出来，生成 dom 节点 （浏览器中）， 生成字符串（ssr）

## 内核调用关系图

内核中 3 个包的主要职责和调用关系图如下：

![调用架构图](https://19ba.oss-cn-hangzhou.aliyuncs.com/blog/img/core-packages.08e2adc3.png)

## 小结

react 暴露主要接口， react-dom 负责虚拟 dom 转实际 dom 渲染相关， react-reconciler 协调任务，scheduler 负责调度；
