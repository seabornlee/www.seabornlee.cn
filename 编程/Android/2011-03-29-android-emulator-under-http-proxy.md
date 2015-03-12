---
title: android模拟器通过代理上网的三种方法
author: SeabornLee
layout: post
permalink: /2011/03/29/android-emulator-under-http-proxy
categories:
  - Android
--- 
1.
```shell
emulator -avd your_avd_name -http-proxy http:// username :password @ ip :port
```
2.
	Adb shell 下执行：
```shell
sqlite3 /data/data/com.android.providers.settings/databases/settings.db "INSERT INTO system VALUES(99,'http_proxy',' ip :port')"
```
3.
启动模拟器，然后进入 settings->Wireless controls->Mobile networks->Access Point Names 
然后打开出现在列表中的access point; 
然后下面这样设置： 
```shell
- Proxy : your proxy address  
- Port : your proxy port  
- Username : your username if needed, or <Not set>  
- Password : your password if needed, or <Not set>
```