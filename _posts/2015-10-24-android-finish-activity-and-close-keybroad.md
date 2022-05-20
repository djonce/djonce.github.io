---
title: android Activity关闭键盘
layout: post
date: 2015-10-24 11:38:24
---

> 提交完数据后，需要立即finish掉Activity，并关闭键盘
> 4.4 以上的系统可以在完成正常的关闭， 4.4以下的不能直接的，activity关闭了 键盘并不能关闭。

因此需要调用延时的关闭activity

{% highlight ruby %}
runUiThreadDelay(new Runnable(){
     @Override 
     public void run() { 
        finish();     
     } }, 300L);

{% endhighlight %}   

> 关闭activity方法

{% highlight ruby %}

@Override 
public void finish() {
     closeSoftKeyBoard();
      super.finish();
 }

{% endhighlight %}


> 关闭键盘方法
{% highlight ruby %}

private void closeSoftKeyBoard() {
     hideSoftInput(activity);
  }

public static boolean hideSoftInput(Activity activity) {
     if(activity.getCurrentFocus() != null) {
        return imm.hideSoftInputFromWindow(activity.getCurrentFocus().getWindowToken(), 0);
    } else {
         return false;
    } 
}

{% endhighlight %}

> 主线线程延时任务

{% highlight ruby %}

public void runUiThreadDelay(Runnable runnable, long delayMillis) { 
    getHandler().postDelayed(runnable, delayMillis);
 }

public Handler getHandler() {
     if (mHandler == null) { 
             mHandler = new Handler(Looper.getMainLooper());
     }
      return mHandler;
 }

{% endhighlight %}


