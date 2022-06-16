---
title: How use tools:overrideLibrary in a build.gradle file
tags: 
layout : post
---

引用了一个同包名的library,编译时报错
```
Error:Execution failed for task ':app:processDebugManifest'.
> Manifest merger failed : uses-sdk:minSdkVersion 8 cannot be smaller than version 9 declared in library [com.alipay.euler:andfix:0.5.0] /Users/apple/Documents/GitHub/AndFixDemo/app/build/intermediates/exploded-aar/com.alipay.euler/andfix/0.5.0/AndroidManifest.xml
  	Suggestion: use tools:overrideLibrary="com.alipay.euler.andfix" to force usage
```

![]({{site.baseurl}}/images/how-use-override-library/61366099-399A-481E-83CE-046A2FC7B19C.png)


```
manifest 标签下添加 
xmlns:tools="http://schemas.android.com/tools"

uses-sdk 标签下添加
tools:overrideLibrary="you library package"

```