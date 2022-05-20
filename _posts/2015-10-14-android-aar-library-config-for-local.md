---
title: android aar文件本地为架包
layout: post
date: 2015-10-14 18:32:21
---

### 配置如下

{% highlight ruby %}

android {
.....
    repositories {
          flatDir {
               dirs   'libs'
          }
     }

.......
}
{% endhighlight %}   

其他androidstudio工程引用aar包

①.将aar包复制到lib目录下

②.配置build.gradle文件：

加入

{% highlight ruby %}

repositories {
        flatDir {
        dirs 'libs'
    }

compile(name:'arrname', ext:'aar')
{% endhighlight %}   
