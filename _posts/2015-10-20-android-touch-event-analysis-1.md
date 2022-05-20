---
title: Android touch事件分发分析(一)
layout: post
date: 2015-10-20
---

主要分析ViewGroup与View的点击屏幕的touch事件




点击中间CView是的事件分发日志
```
 D/AViewGroup: dispatchTouchEvent
 D/AViewGroup: onInterceptTouchEvent
 D/BViewGroup: dispatchTouchEvent
 D/BViewGroup: onInterceptTouchEvent
 D/CView: dispatchTouchEvent
 D/CView: onTouchEvent
 D/BViewGroup: onTouchEvent
 D/AViewGroup: onTouchEvent
```

AViewGroup dispatchTouchEvent设置返回**ture**;
```
D/AViewGroup: dispatchTouchEvent
```
AViewGroup onInterceptTouchEvent设置返回ture;
```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/AViewGroup: onTouchEvent
```

AViewGroup onTouchEvent设置返回ture;
```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/CView: onTouchEvent
D/BViewGroup: onTouchEvent
D/AViewGroup: onTouchEvent

D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onTouchEvent
```

BViewGroup dispatchTouchEvent设置返回ture;
```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
```

BViewGroup onInterceptTouchEvent设置返回ture;
```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/BViewGroup: onTouchEvent
D/AViewGroup: onTouchEvent
```

BViewGroup onTouchEvent设置返回ture;
```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/CView: onTouchEvent
D/BViewGroup: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onTouchEvent
```

CView dispatchTouchEvent设置返回ture;

```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
```

CView onTouchEvent设置返回ture;

```
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/CView: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/CView: onTouchEvent
D/AViewGroup: dispatchTouchEvent
D/AViewGroup: onInterceptTouchEvent
D/BViewGroup: dispatchTouchEvent
D/BViewGroup: onInterceptTouchEvent
D/CView: dispatchTouchEvent
D/CView: onTouchEvent
```

#### 总结：
正常情况下分发的流程是：
**AViewGroup -> BViewGroup -> CView。**事件传递的时候，先执行dispatchTouchEvent()方法, 再执行onInterceptTouchEvent()方法。

事件处理流程是：
**CView -> BViewGroup -> AViewGroup。**事件处理都是执行onTouchEvent()方法。

初始情况下，返回值都是false。
   
事件分发传递的返回值： Ture, 拦截, 不继续; False, 不拦截,继续分发。   
事件处理的返回值： Ture, 处理了,不需要上级ViewGroup处理; False, 事件抛给上一级ViewGroup处理；

