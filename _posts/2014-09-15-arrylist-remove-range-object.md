--- 
layout : post
title: "ArrayList 移除指定区间的数据"
date: 2014-09-15 11:10:51
---


ArrayList 中自带一个** removeRange(int formIndex, int toIndex) ** 不过是**protected**的。

### 示例代码

{% highlight css %}

public class RangeArrayList extends ArrayList {
    public void removeRange(int fromIndex, int toIndex) {
            super.removeRange(fromIndex, toIndex);
         }
    
    }
{% endhighlight %}

继承ArrayList 重新removeRange 就可以了，使用方式与ArrayList一致。


