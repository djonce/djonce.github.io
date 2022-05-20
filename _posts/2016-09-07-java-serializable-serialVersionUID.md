---
title: Java Serializable serialVersionUID问题
layout: post
date: 2016-09-07 10:38:24
---

>前言：项目升级新版本时，保存在SharedPreferences中用户信息丢失了。升级上来调试的时候取不到值，log中也没有看到其它信息，先后查看了上来的文件都是存在的，于是定位到取值的方法上面，进过调试发现我取对象时出现(java.io.InvalidClassException: ...; local class incompatible: stream classdesc serialVersionUID = xxL, local class serialVersionUID = xxxL)异常。

> SharedPreferences中保存User 我是以对象的方式保存的。

{% highlight ruby %}
public void put(String key, Object ser, Cipher cipher) {
        try {
            Log.i(TAG, key + " put: " + ser);
            if(ser == null) {
                this.sp.edit().remove(key).commit();
            } else {
                byte[] e = ByteUtil.objectToByte(ser);
                if(cipher != null) {
                    e = cipher.encrypt(e);
                }

                this.put(key, HexUtil.encodeHexStr(e));
            }
        } catch (Exception var5) {
            var5.printStackTrace();
        }

    }
{% endhighlight %}

> SharedPreferences中取出*User*的Object对象进行强制转换为User。

{% highlight ruby %}
public Object get(String key, Cipher cipher) {
        try {
            String e = this.get(key, (String)null);
            if(e == null) {
                return null;
            } else {
                byte[] bytes = HexUtil.decodeHex(e.toCharArray());
                if(cipher != null) {
                    bytes = cipher.decrypt(bytes);
                }

                Object obj = ByteUtil.byteToObject(bytes);
                Log.i(TAG, key + " get: " + obj);
                return obj;
            }
        } catch (Exception var6) {
            var6.printStackTrace();
            return null;
        }
    }

{% endhighlight %}

问题已经很明确了，类转换异常
查了一下 User的修改记录，里面加了个没有使用到的方法，导致User的序列化编号不一致。此时就到值了转化异常，取不到值。


