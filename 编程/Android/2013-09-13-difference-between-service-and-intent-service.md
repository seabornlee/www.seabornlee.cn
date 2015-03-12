---
date: 2013-09-13 08:06
status: public
tags: 'Android,code review'
title: Service和IntentService的区别
---

#### code review
周三下午接近5点的时候，我熟练地将投影仪打开并接上电脑，小伙伴们敲完git push，如释重负地敲下回车，很快在墙前围成一个半圆，吃着饼干水果，开始每天的code review。

当我介绍我的一段代码时，一个小伙伴问：“Service和IntentService有什么区别？”，我脸一红，尴尬地说：“我也不清楚。”这时另一位小伙伴一边咬李子一边说：“Service运行在主线程里，IntentService运行在单独的线程里。”就这样，知识得到了传播，Code Review继续进行。

这两天开发工作如常进行着，但那尴尬的一幕却深深刻在了我的脑海里。虽然我是Android新手，但作为一个给自己贴着“靠谱”标签的程序员，怎么能commit连自己都不理解的代码呢？更何况在客户现场，我们都是所谓的“咨询师”，除了要给客户解决技术问题，更要传播正确的做事态度和方法。所以我必须搞清楚这个知识点，并且牢牢记住这件事，避免再次发生。

#### 文档和代码
首先我查阅文档，发现IntentService其实是Service的子类。
```
public abstract class IntentService extends Service
```

#### 总结