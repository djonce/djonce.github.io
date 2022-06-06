---
layout: post
title: android gradle manifestPlaceholders简单实用
date: 2016-06-10 10:38:24
---


> 项目开发中，时常要切换到不同的服务器，app包的配置也是时常变化
>


### AndroidManifest.xml

{% highlight ruby %}

<!-- 配置的第三方参数属性 -->
<meta-data
    android:name="PUSH_APPID"
        android:value="${PUSH_APPID}" />
<meta-data
    android:name="PUSH_APPKEY"
    android:value="${PUSH_APPKEY}" />
<meta-data
    android:name="PUSH_APPSECRET"
    android:value="${PUSH_APPSECRET}" />

{% endhighlight %}


###  主项目build文件

{% highlight ruby %}

buildTypes {
    debug {
            // 配置
                buildConfigField "String", "API_HOST_V1", "\"http://test.19ba.cn/api/user.json\""
                manifestPlaceholders = [
                        "PUSH_APPID"     : "RwMRBLFggssH9VXPhG4EGwA3",
                        "PUSH_APPKEY"    : "NI8gTsBtV3h74uYkIo3JCv2",
                        "PUSH_APPSECRET" : "UzLbqt5HUZ6lAUAzFRlKn"
                        ]
            }
    release {
            minifyEnabled true
                shrinkResources true
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'        // 配置
                buildConfigField "String", "API_HOST_V1", "\"http://release.19ba.cn/api/user.json\""
                manifestPlaceholders = [
                        "PUSH_APPID"     : "RwMRBLFggHs9VXPhG4EGwA3",
                        "PUSH_APPKEY"    : "NI8gTBtV3sh74uYkIo3JCv2",
                        "PUSH_APPSECRET" : "UzLbqt5HUZ6lAUAzFRlKn"        ]
            }
    }

{% endhighlight %}


