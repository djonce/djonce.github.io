---
layout: post
title: "jekyll docker 部署"
description: ""
category: jekyll
tags: []
---
{% include JB/setup %}


### 文档
https://github.com/envygeeks/jekyll-docker/blob/master/README.md

### 快速启动命令

``` shell

docker run --rm \
  --volume="$PWD:/srv/jekyll:Z" \
  --publish [::1]:4000:4000 \
  -it \
  jekyll/jekyll \
  jekyll serve -w
```


docker run --rm -p 4000:4000 -v $PWD:/srv/jekyll/ -it jekyll/jekyll:3.5 jekyll server




adduser jekyll --password jekyll000

chmod -v u+w /etc/sudoers 修改为可编辑

vi /etc/sudoers 在root下添加一行jekyll
root    ALL=(ALL)       ALL
jekyll  ALL=(ALL)       ALL

chmod -v u-w /etc/sudoers 再修改为不可编辑

sudo groupadd docker 添加docker用户组
sudo gpasswd -a jekyll docker 将jekyll用户加入到docker用户组中
newgrp docker 更新用户组

chown -R jekyll:jekyll /home/jekyll/ 更新jekyll所有文件所属 

su jekyll 使用jekyll登录继续下一步


docker run -it --rm -p 4000:4000 -v /home/jekyll/www:/srv/jekyll/ jekyll/jekyll:4.1.0 jekyll server -w




docker run -it --rm -p 4000:4000 -v /home/jekyll/www/temp:/srv/jekyll/ jekyll/jekyll:4.1.0 jekyll server


docker run --rm --volume="$PWD:/srv/jekyll" -it jekyll/jekyll:3.5 jekyll new .

docker run --name newblog --volume="$PWD:/srv/jekyll" -p 4000:4000 -it jekyll/jekyll:3.5 jekyll serve --watch --drafts
