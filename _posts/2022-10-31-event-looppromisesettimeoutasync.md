---
layout: post
title: "事件循环（Event Loop）promise、setTimeout、async的先后执行顺序"
description: ""
category:
tags: []
---

{% include JB/setup %}

javaScript 的特点就是**单线程**，在这个线程中拥有唯一的一个事件循环。

- JS 分为同步任务和异步任务，同步任务都在主线程上执行。前一个任务执行完毕之后，执行后一个任务，形成一个**执行栈**；
- 主线程之外，事件触发线程管理着一个任务队列，异步任务会被主线程挂起，不会进入主线程，而是进入任务队列。只要异步任务有了运行结果，就会在队列任务中放置一个事件；
- 一旦执行栈中所有的同步任务执行完毕后，系统就会读取任务队列，将可运行的异步任务添加到可执行栈中，开始执行。

**为什么 JS 是单线程的？？**

JS 的主要用途就是与用户交互，操作 DOM，假如 JS 同时有两个线程，一个线程中在某个 DOM 节点上添加内容，另一个线程需要执行删除该节点操作，就会产生冲突。

事件循环机制告诉我们 JavaScript 的执行顺序。

单线程意味着所有任务都需要排队，前一任务结束，才会执行后一个任务，如果前一个任务耗时很长，后一个任务就不得不一直等着。

JS 引擎执行异步代码不用等待，是因为有事件队列和事件循环。

**事件循环**是指主线程重复从事件队列中取消息、执行的过程。指整个执行流程。

**事件队列**是一个存储着待执行任务的序列，其中的任务严格按照时间先后顺序执行，排在队头的任务会率先执行，而排在队尾的任务会最后执行。

![1313634-20190727155718269-1808948786.png](https://oss.19ba.cn/public/20221031/euk498as.png)

事件队列：

- 一个线程中，事件循环是唯一的，但是任务队列可以有多个；
- 任务队列又分 macro-task(宏任务)和 micro-task(微任务)；
- macro-task 包括：script（整体代码）、setTimeout、setInterval、setImmediate、I/O、UI rendering；
- micro-task 包括：process.nextTick, Promise, Object.observe(已废弃), MutationObserver(html5 新特性)
- setTimeout/Promise 等称为任务源，而进入任务队列的是他们制定的具体执行任务；**来自不同任务源的任务会进入到不同的任务队列**，其中 setTimeout 与 setInterval 是同源的；

宏任务可以理解成每次执行栈执行的代码就是一个宏任务。

**事件循环运行机制**

（1）执行一个宏任务（栈中没有就从事件队列中获取）

（2）执行过程中如果遇到微任务，就将它添加到微任务的任务队列中；

（3）宏任务执行完毕后，立即执行当前微任务队列的所有微任务；

（4）当前微任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染；

（5）渲染完毕后，JS 线程继续接管，开始下一个宏任务。

例：

```
    async function async1() {
        console.log("async1 start");  //(2)
        await  async2();
        console.log("async1 end");   //(6)
    }
    async  function async2() {
        console.log( 'async2');   //(3)
    }
    console.log("script start");  //(1)
    setTimeout(function () {
        console.log("settimeout");  //(8)
    },0);
    async1();
    new Promise(function (resolve) {
        console.log("promise1");   //(4)
        resolve();
    }).then(function () {
        console.log("promise2");    //(7)
    });
    console.log('script end');//(5)
```

先按顺序执行同步代码   从‘script start‘开始，

执行到 setTimeout 函数时，将其回调函数加入队列(此队列与 promise 队列不是同一个队列，执行的优先级低于 promise。

然后调用 async1()方法，await async2();//执行这一句后，输出 async2 后，await 会让出当前线程，将后面的代码加到任务队列中，然后继续执行 test()函数后面的同步代码

继续执行创建 promise 对象里面的代码属于同步代码，promise 的异步性体现在 then 与 catch 处，所以 promise1 被输出，然后将 then 函数的代码加入队列，继续执行同步代码，输出 script end。至此同步代码执行完毕。

开始从队列中调取任务执行，由于刚刚提到过，setTimeout 的任务队列优先级低于 promise 队列，所以首先执行 promise 队列的第一个任务，因为在 async 函数中有 await 表达式，会使 async 函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数并返回解决结果。

所以先执行 then 方法的部分，输出 promise2，然后执行 async1 中 await 后面的代码，输出 async1 end。。最后 promise 队列中任务执行完毕，再执行 setTimeout 的任务队列，输出 settimeout。

setTimeout(fn,0)的含义是指某个任务在主线程最早可得的空闲时间执行。它在“任务队列”的尾部添加一个事件，因此要等到同步任务和“任务队列”现有的时间处理完才会得到执行。

**按照事件循环机制分析以上代码运行流程：**

1. 首先，事件循环从宏任务(macrotask)队列开始，这个时候，宏任务队列中，只有一个 script(整体代码)任务；当遇到任务源(task source)时，则会先分发任务到对应的任务队列中去。

2. 然后我们看到首先定义了两个 async 函数，接着往下看，然后遇到了 \`console\` 语句，直接输出 \`script start\`。输出之后，script 任务继续往下执行，遇到 \`setTimeout\`，其作为一个宏任务源，则会先将其任务分发到对应的队列中。

3. script 任务继续往下执行，执行了 async1()函数，前面讲过 async 函数中在 await 之前的代码是立即执行的，所以会立即输出\`async1 start\`。  
   遇到了 await 时，会将 await 后面的表达式执行一遍，所以就紧接着输出\`async2\`，然后将 await 后面的代码也就是\`console.log('async1 end')\`加入到 microtask 中的 Promise 队列中，接着跳出 async1 函数来执行后面的代码。

4. script 任务继续往下执行，遇到 Promise 实例。由于 Promise 中的函数是立即执行的，而后续的 \`.then\` 则会被分发到 microtask 的 \`Promise\` 队列中去。所以会先输出 \`promise1\`，然后执行 \`resolve\`，将 \`promise2\` 分配到对应队列。

5. script 任务继续往下执行，最后只有一句输出了 \`script end\`，至此，全局任务就执行完毕了。  
   根据上述，每次执行完一个宏任务之后，会去检查是否存在 Microtasks；如果有，则执行 Microtasks 直至清空 Microtask Queue。  
   因而在 script 任务执行完毕之后，开始查找清空微任务队列。此时，微任务中， \`Promise\` 队列有的两个任务\`async1 end\`和\`promise2\`，因此按先后顺序输出 \`async1 end，promise2\`。当所有的 Microtasks 执行完毕之后，表示第一轮的循环就结束了。

6.  第二轮循环依旧从宏任务队列开始。此时宏任务中只有一个 \`setTimeout\`，取出直接输出即可，至此整个流程结束。
