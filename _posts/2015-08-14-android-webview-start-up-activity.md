---
title: WebView中网页中自定义协议拉起指定的acitivity
layout: post
date: 2015-08-14 20:23:34
---

### 步骤：
#### 1.定义一个Activity, 例如： WebViewActivity 在AndroidManifest 中定义自己的拉起协议   

{% highlight ruby %}
<activity android:name=".WebViewActivity">
       <intent-filter>
          <action android:name="android.intent.action.VIEW"/>
          <category android:name="android.intent.category.BROWSABLE"/>
          <category android:name="android.intent.category.DEFAULT"/>
          <date android:scheme="wva"
                android:host="action"
                android:path="/webview"/>
        </intent-filter>
</activity>
{% endhighlight %}

#### 2.在网页中<a>标签中或其他   
{% highlight ruby %}
<a class="test" href="wva://action/webview ">测试</a>
{% endhighlight %}

 ##### 注：WebView中 setWebViewClient不用设置 webViewClient为null时系统会设置NullWebViewClient
          核心代码：  WebViewContentsClientAdapter

{% highlight ruby %}
void setWebViewClient(WebClient client) {
     if(client != null){
        mWebViewClient = client; 
      } else{
        mWebViewClient = new NullWebViewClient(); 
      }
}
{% endhighlight %}

#### NullWebViewClient 处理未能识别的协议   shouldOverrideUrlLoading 方法   
{% highlight ruby %}
public boolean shouldOverrideUrlLoading(WebView view, String url){
Intent intent;
// Perform generic parsing of the URI to turn it into an Intent.

try {
    intent = Intent.parseUri(url, Intent.URI_INTENT_SCHEME);
} catch (URISyntaxException ex) {
    Log.w(TAG, "Bad URI " + url + ": " + ex.getMessage());
    return false;
}
// Sanitize the Intent, ensuring web pages can not bypass browser
// security (only access to BROWSABLE activities).
intent.addCategory(Intent.CATEGORY_BROWSABLE);
intent.setComponent(null);

// Pass the package name as application ID so that the intent from the
// same application can be opened in the same tab.

intent.putExtra(Browser.EXTRA_APPLICATION_ID,
        view.getContext().getPackageName());
try {
    view.getContext().startActivity(intent);
} catch (ActivityNotFoundException ex) {
    Log.w(TAG, "No application can handle " + url);
    return false;
}
return true;
}
{% endhighlight %}
