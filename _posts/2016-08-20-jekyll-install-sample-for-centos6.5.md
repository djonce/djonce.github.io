---
title: Linux(Centos6.5)安装jenkyll 
layout: post
date: 2016-08-20 22:45:23
---

安装jekyll之前要先安装依赖库ruby,gem

### 步骤
1.安装前先安装gcc
```
yum install gcc
```
2.推荐使用rvm来安装ruby和gem(https://ruby-china.org/wiki/install_ruby_guide)最后安装成功。然后需要重启一下终端，甚至需要重启一下系统，然后查rvm， ruby， gem版本信息如下：
```
[root@iZ231xhruwmZ jonce]# rvm -v
rvm 1.27.0 (latest) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
[root@iZ231xhruwmZ jonce]# ruby -v
ruby 2.3.0p0 (2015-12-25 revision 53290) [x86_64-linux]
[root@iZ231xhruwmZ jonce]# gem -v
2.5.1

```

3.安装jekyll
```
gem install jekyll
```
注：如系统报错：Unable to download data from https://rubygems.org/ 等类似错误，请更换ruby的源。我用的是淘宝的ruby源
```
删除默认的官方源
gem sources -r https://rubygems.org/
添加淘宝源
gem sources -a https://ruby.taobao.org/
查看当前源
gem sources -l # https://ruby.taobao.org
```
成功安装后，查看：
```
[root@iZ231xhruwmZ jonce]# jekyll -v
jekyll 3.3.1
```
站点目录下运行服务：
```
jekyll serve
```

写完文章上传到_posts目录下运行下面的命令发布
```
发布的目录下 jekyll build
```


