---
title: Android 双击返回键退出应用程序
layout: post
date: 2013-08-10 16:23:12
---

{% highlight ruby %}
long exitTime = 0;
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    if (KeyEvent.KEYCODE_BACK == keyCode) {
        if (System.currentTimeMillis() - exitTime > 2000) {
            toast("再按一次退出程序");
            // 将系统当前的时间赋值给exitTime
            exitTime = System.currentTimeMillis();
        } else {
            finish();
        }
        return true;
    }
    return super.onKeyDown(keyCode, event);
}
{% endhighlight %}
