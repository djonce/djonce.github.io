---
layout: post
title: "HTTP 协议小结"
description: ""
category:
tags: []
---

{% include JB/setup %}

## 初识 HTTP

HTTP 全名为超文本传输协议，是应用层的协议。语意简单，可扩展，协议无状态。

### 协议分析

- HTTP/0.9 只能相应 GET /page.html，只能响应 HTML 文档。
- HTTP/1.0 增加了 Header，状态吗、多种文档类型。
- HTTP/1.1 开始标准化，链接复用、缓存、内容协商等。
- HTTP/2 增加优异表现，二进制，压缩，服务器推送。
- HTTP/3 草案阶段。

### 报文内容

- startline
- header
- Empty-line
- body

### 请求类型

- 安全的方法：不会修改服务器的数据，如 GET, HEAD, OPTIONS
- 幂等的方法：同样请求执行多次效果相同，服务器状态相同。如 GET HEAD OPTIONS PUT DELETE

### 状态码

- 1xx：继续处理
- 2xx：成功（200 OK）
- 3xx：重定向，需要接着操作 （301，302）
- 4xx：客户端错误（401，404）
- 5xx：服务端错误（500，504）

### 请求头

| ----------------- | ----------------- |
| Accept | 允许的类型|
| Content-Type |body 的类型|
|Cache-Control |是否允许缓存|
| If-Modified-Since| 文件是否变动，用于缓存|
| Expires |缓存有效时间|
| Max-age| 缓存使用时间|
|If-None-Match |服务端 ETag，匹配文件内容|
| Cookie |同域访问带上的信息|
| Referer |来源 URL|
|Origin |最初请求哪里来的（和 Referer 关系？）|
|User-Agent | 用户客户端的必要信息（UA）|

### 响应头

| ----------------- | ----------------- |
|Content-Type |同上|
|… |…|
|Access-Control-Allow-Origin |允许哪些 origin 访问这个接口|

### 缓存

- 强缓存：Expires（到期时间）+ Cache-Control（可缓存性）

- 协商缓存：Etag/If-None-Match（利用指纹进行对比信息）+ Last-Modified/If-Modified-Since（利用时间进行对比）

### 请求

尝试使用强缓存（如有结束）

尝试看看 ETag（如有，发起 If-None-Match 请求，304 可以用缓存）

尝试看看 Last-Modified（如有，发起 If-Modified-Since，304 可以用缓存）

### Cookie

都是键值对信息

Name, Expires, Path, Domain

secure: 是否 HTTPS

HttpOnly: js 不能用

SameSite：跨域相关

### HTTP/2

- 提出了帧（将一个 HTML 请求拆分成为若干帧）。
- 帧内压缩减小体积。帧外交错传递，属于同一个 HTML 的帧拼接为一条信息。
- 每一个来源只需要一个连接，所有请求都走这一个来源，利用帧拼接防止重叠。
- 接收方可以主动组织继续发送。
- 服务器可以主动将没有请求的文件 push 给接收端。（例如 HTML 文件附加的 JavaScript 文件）（注意服务器推送能力不能和 CDN 等同时利用，有些时候反而降低性能）
