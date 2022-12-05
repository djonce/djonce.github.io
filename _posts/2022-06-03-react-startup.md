---
layout: post
title: "react应用启动过程"
description: ""
category:
tags: [react]
---

{% include JB/setup %}

## React 启动模式

在 react@17.0.2 源码中 ，有**3**种启动模式。基本如下：

1、legacy 模式：ReactDOM.render(<app/>, rootNode).

2、Blocking 模式： ReactDOM.createBlockingRoot(rootNode).render(<App/>)

3、Concurrent 模式： ReactDOM.createRoot(rootNode).render(<App/>)

## React 启动流程

在调用入口函数之前 ， ReactElement(<App/>) 和 DOM 对象 div#root 之间没有关联。

### 创建全局对象

无论在 Legacy, Concurrent 或者 Blocking 模式， react 在初始化时，都会创建 3 个全局对象

1、 ReactDOMRoot 对象 （ 属于 react-dom 包)

- 该对象暴露 render, unmount 方法， 通过调用该实例的 render 方法， 可以引导 react 应用的启动。

2、 FiberRoot 对象 (属于 react-reconciler 包)

- 作为 react-reconciler 在运行过程中的全局上下文，保存 fiber 构建过程中所依赖的全局状态。
- 大部分实例变量来存储 fiber 构造循环过程过程中的各种状态。

3、HostRootFiber (属于 react-reconciler 包)

- 这是 react 应用第一个 Fiber 对象，是 Fiber 树的根节点， 节点类型是 HostRoot.

![创建流程图](https://19ba.oss-cn-hangzhou.aliyuncs.com/blog/img/function-call.fc60389d.png)

### 创建 ReactDOMRoot 对象

**3** 种模式启动的 API 有所不同，最终都是通过 new ReactDOMRoot 或 ReactDOMBlockingRoot 的实例。创建的过程通过 RootTag 参数区分。

```
export type RootTag = 0 | 1 | 2;

export const LegacyRoot = 0;
export const BlockingRoot = 1;
export const ConcurrentRoot = 2;

```

**legacy 模式**

legacy 模式直接调用 ReactDOM.render, 调用的是 **ReactDOMLegacy.js** 中的 render 方法，后续调用 **legacyRenderSubtreeIntoContainer**

```
function legacyRenderSubtreeIntoContainer(
  parentComponent: ?React$Component<any, any>,
  children: ReactNodeList,
  container: Container,
  forceHydrate: boolean,
  callback: ?Function,
) {
  let root: RootType = (container._reactRootContainer: any);
  let fiberRoot;
  if (!root) {
    // 初次调用, root还未初始化, 会进入此分支
    //1. 创建ReactDOMRoot对象, 初始化react应用环境
    root = container._reactRootContainer = legacyCreateRootFromDOMContainer(
      container,
      forceHydrate,
    );
    fiberRoot = root._internalRoot;
    if (typeof callback === 'function') {
      const originalCallback = callback;
      callback = function() {
        // instance最终指向 children(入参: 如<App/>)生成的dom节点
        const instance = getPublicRootInstance(fiberRoot);
        originalCallback.call(instance);
      };
    }
    // 2. 更新容器
    unbatchedUpdates(() => {
      updateContainer(children, fiberRoot, parentComponent, callback);
    });
  } else {
    // root已经初始化, 二次调用render会进入
    // 1. 获取FiberRoot对象
    fiberRoot = root._internalRoot;
    if (typeof callback === 'function') {
      const originalCallback = callback;
      callback = function() {
        const instance = getPublicRootInstance(fiberRoot);
        originalCallback.call(instance);
      };
    }
    // 2. 调用更新
    updateContainer(children, fiberRoot, parentComponent, callback);
  }
  return getPublicRootInstance(fiberRoot);
}

```

继续跟踪 legacyCreateRootFromDOMContainer. 最后调用 new ReactDOMBlockingRoot(container, LegacyRoot, options);

```
function legacyCreateRootFromDOMContainer(
  container: Container,
  forceHydrate: boolean,
): RootType {
  const shouldHydrate =
    forceHydrate || shouldHydrateDueToLegacyHeuristic(container);
  return createLegacyRoot(
    container,
    shouldHydrate
      ? {
          hydrate: true,
        }
      : undefined,
  );
}

export function createLegacyRoot(
  container: Container,
  options?: RootOptions,
): RootType {
  return new ReactDOMBlockingRoot(container, LegacyRoot, options); // 注意这里的LegacyRoot是固定的, 并不是外界传入的
}
```

通过以上分析， **legacy** 模式下调用 ReactDOM.render 有 2 个核心步骤：
1、创建 ReactDOMBlockingRoot 实例， 初始化 react 应用环境。
2、调用 **updateContainer** 进行更新。

参考：
https://17.reactjs.org/docs/concurrent-mode-adoption.html#why-so-many-modes
