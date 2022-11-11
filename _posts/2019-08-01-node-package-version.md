---
layout: post
title: "node package versions"
description: ""
category:
tags: [nodejs]
---

{% include JB/setup %}

## node package version

13.4.6

major 13, minor:4, patch: 6

## major 、minor 、patch

major : **主版本** - 做了不会向下兼容的改动

minor : **次版本号** - 做了会向下兼容的功能新增

patch : **修订号** - 做了会向下兼容的问题修正

## npm 版本符号

^ : 锁定 major

~ : 锁定 minor

空 : 锁定 patch

- : 最新版本

## package version 更新命令

升级 patch 版本

```
 npm version patch
```

升级 minor 版本

```
 npm version minor
```

升级 major 版本

```
 npm version major
```
