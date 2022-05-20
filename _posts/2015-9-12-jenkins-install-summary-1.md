---
title: jenkins使用小结 
layout: post
date: 2015-09-12
---

> Jenkins 运行大致流程

jenkins通过插件svn(git或其它)获取项目源码，拉取到的源码放在Jnekins安装目录job目录下;通过安装的Ant、Maven、Gradle等实现源码的打包，在此过程可以设置代码的检查机制进行代码生成分析;通过项目的审查后，编译打包成相应的发布格式(Web可以通过shell,将项目打包成tar/android打包成apk等);连接远程服务器部署项目(将打包好的代码发到目标服务器)
#### 总结：
```
1.获取源码到Jenkins
2.在Jenkins进行代码审查、打包等
3.在线发布到目标服务器运行环境
```

