---
date: 2014-10-19 10:27
status: public
title: 升级Yosemite的常见问题解决办法
---

![](~/Screen Shot 2014-10-19 at 13.35.15.png)

前两天Apple终于发布了稳定版的Yosemite，相信大家等了这么久，都第一时间开始升级了吧。
最近总看到有朋友在问一些升级的问题，因为我早在Beta版的时候就已经升级，经历过一些问题，写出来希望能对大家有些帮助。

### 耐心等待“最后x分钟”
我在第一次升级的时候，一路都很顺畅，直到最后显示还需要“2分钟”，然后我等了超过1小时，于是我认为升级已经失败，便强行重启了，导致系统无法正常启动，只能重新来过。花了很多时间后，最后终于知道问题在哪里，在升级过程中，可以按`CMD+L`打开Log窗口，你能看到其实升级程序一直在运行，只不过在处理HomeBrew安装的程序，因为我是Home Brew重度用户，这块需要很长时间，唯一能做的就是耐心等待，出去跑一个半程马拉松再回来也许是个好选择。

### 修改plist文件才能运行Java应用
升级后你会发现Eclipse，IntelliJ等应用都不能启动了，你会看到如下的错误。
>To open "IntelliJ IDEA" you need to install the legacy Java SE 6 runtime.

![](~/Screen Shot 2014-10-19 at 10.47.20.png)
这是因为系统升级后JVM升级到了`1.7`，而应用指定了依赖`1.6*`的版本。
这时你有两种选择：
1 下载安装JDK6并指定JAVA_HOME
2 修改plist文件，指定依赖的JVM为1.7*

第一种的好处是一次就能解决全部应用的问题，第二种需要逐个应用去修改。
这里重点说一下第二种办法，以IntelliJ IDEA为例：
1 用文本编辑器打开`/Applications/IntelliJ IDEA 13.app/Contents/Info.plist`
2 搜索`JVMVersion`，将其值改为`1.7*`
3 再次运行应用即可看到应用成功运行

[点击查看演示](http://www.seabornlee.cn/view?intellij.gif)

### HAX is not working
如果你使用Android Emulator，你可能会发现，启动时显示："HAX is not working...."
这时你只需在Terminal中执行：`sudo nvram boot-args="kext-dev-mode=1"` ，然后重启即可。

### Postgresql无法连接
升级后你可能会发现无法连接Postgresql始终无法连接，重新安装也能解决。经过试验，将数据目录删除重建即可。
`initdb -D /usr/local/pgsql/data`。

### 设置Handoff功能
升级完后第一时间就开始尝试Handoff，结果却让人非常失望，但经过一番研究之后终于成功启用了此功能。
首先除了Mac OS要是Yosemite外，手机必须是iOS8。
然后需要以下几步：
1 Yosemite和iOS上的iCloud登录同一个Apple ID；
2 Mac和iOS上都要打开蓝牙，无需配对；
3 打开Yosemite的`System Preferences->General`，勾选“Allow Handoff between this Mac and your iCloud devices”
4 打开iOS的`Settings->General->Handoff & Suggested App`，启用Handoff。

完毕，这时你在手机上打开Safari，你的Mac的Dock最左边就会多出一个浏览器图标，你在Mac上的Safari打开一个网页，手机的锁屏界面也会显示一个Safari图标。更多有趣的功能请自行搜索。

### 在Mac上打电话
相信大家都对发布会上演示的使用Mac打电话期待已久，这个也不是升完级就能直接使用的。需要进行简单的设置：
1 打开iOS的`Settings->FaceTime`，启用`iPhone Cellular Calls`；
2 打开Yosemite上的FaceTime的设置，勾选`iPhone Cellular Calls`。

一开始我错误地认为这个功能是Handoff中的一部分，但其实它们没有半毛钱关系，而且也无需蓝牙连接，无需3G，只要手机与电脑在同一Wifi下即可。另外，收发短信的功能据说要等升了iOS 8.1后才能用。

就这么多，不用谢，请叫我雷锋。