---
title: Android 欢迎页出现白屏问题 
layout: post
date: 2015-08-19
---

欢迎页出现白屏问题
刚启动欢迎页面，出现白屏问题
通过设置主题的背景解决

### 背景用Drawable实现
#### 1.定义drawable
```
<selector   /> ........StateListDrawable
<level-list /> ........LevelListDrawable
<layer-list /> ........LayerDrawable
<transition /> ........TransitionDrawable
<color      /> ........ColorDrawable
<shape      /> ........GradientDrawable
<scale      /> ........ScaleDrawable
<clip       /> ........ClipDrawable
<rotate     /> ........RotateDrawable
<animation-list/> .....AnimationDrawable
<inset      /> ........InsetDrawable
<bitmap     /> ........BitmapDrawable
<nine-patch /> ........NinePatchDrawable

```
### xml：
```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
     <item>
         <shape android:shape="rectangle" >
             <solid android:color="@color/app_background" />
         </shape>
     </item>

     <item android:bottom="48dp">
         <bitmap
             android:gravity="center"
             android:src="@drawable/logo" />
     </item>
</layer-list>
```
#### 2.定义Activity的样式
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
   <style
       name="Theme.Default.NoActionBar"
       parent="@android:style/Theme.Holo.Light.NoActionBar" >
       <item name="android:windowBackground">@drawable/login</item>
   </style>
</resources>

```

#### 3.主题设置到指定的Activity上
```
<activity
 android:name="WelcomeActivity"
 android:theme="@style/Theme.Default.NoActionBar"/>

```
