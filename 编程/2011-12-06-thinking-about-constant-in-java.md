---
title: 一个Java常量引发的思考
author: SeabornLee
layout: post
permalink: /2012/02/06/thinking-about-a-constant-in-java
categories:
  - Java
--- 

[TOC]

### 事出有因

农历xx月xx日，是一个特别的日子，因为用户发邮件说：要将上传附件的大小从原来的5MB提高到10M。
这是多么简单多么合理的一个小变更，处理起来是多么的easy...
 
为了完成这个变更，你可能会在数据库中修改一个字段，或者在xml中修改一个数值。
但我们不是，在代码中，我们将这个数字定义为常量:
 
Java代码  
```java
public static final long MAX_SIZE = 5 * 1024 * 1024; 
```
 
那修改也很简单，将5改成10即可。
你可能有自动化的部署环境，但我们只能手工替换class文件。
但是当我们将新的class替换到生产系统上时，发现没有生效，再替换，还是不生效...
就在西二旗城铁站晚高峰快要结束的时候，突然，灵光一闪，我仿佛领悟到了什么，遂将引用这个常量的类也重新编译并替换到生产系统，It works!
为什么我会灵光一闪呢？因为这个问题我并不是第一次遇到了，所以我觉得有必要写一篇文章来纪念一下。
 
我推测原因是编译器在编译时会对常量进行优化，将其替换为实际的值……
为了验证我的想法，我写了两个类来测试，

T1.java

![T1.java](http://dl.iteye.com/upload/attachment/600917/6ebf26ad-a4e7-3a84-830f-b1459d2fe81c.png)
 
T2.java

![T2.java](http://dl.iteye.com/upload/attachment/600919/9ae4253c-cfa2-357f-abcf-6932d3948cda.png) 

然后我编译，执行，删除T2.class，再执行。

![运行结果](http://dl.iteye.com/upload/attachment/600921/543907af-3917-30a7-bd41-62df5e7e7f92.png)

有点出乎意料啊，和预想的结果并不一致……
就在二路汽车快要收工的时候，突然，我灵光又一闪，仿佛又领悟到了什么，遂将 Long 改成 long:

![](http://dl.iteye.com/upload/attachment/600923/600521c7-5073-3ee3-a39a-bfd59bc0844a.png)
 
再次编译，执行，删除T2.class，再执行:

![](http://dl.iteye.com/upload/attachment/600925/c88c9810-3e9a-34ec-854a-45d5e56ecde3.png)

It works again!
 
后来又测试了int, Integer, double, Double, String等类型，
并且使用javap -c T1查看反编译结果发现： 

>**编译器在编译时会对基本数据类型和String类型进行优化。**
  
### 持续改进

除了将配置放到数据库，xml，或者进行自动化部署之外。
针对这个项目，可以做这样的改进，当系统在线上运行时，如果需要修改某个的常量值（基本类型或String类型），那么引用该常量的所有class都应当重新编译。
换句话说：如果A类中使用了B类中的常量（基本类型或String类型），那在系统运行的过程中，你删除掉B.class，不会影响系统的使用。
 
所以如果你不想在后期维护时这么麻烦的话，最好定义变量时使用包装类（Long, Integer等），或者换一种设计方式。
 
比如原来的代码是这样：
HttpUtil类中有一个保存附件的方法
 
Java代码  
```java
public class HttpUtil {   
  public static final long MAX_SIZE = 5 * 1024 * 1024;  
  public static void saveFile(HttpServletRequest req, long maxSize) {...}  
}
 ```
 
在别的模块如信息发布中有上传附件的功能，会调用HttpUtil的保存文件方法
因为没有对附件的特殊限制，所以使用默认的MAX_SIZE即可：
 
Java代码  
```java
…  
HttpUtil.save(req, HttpUtil.MAX_SIZE);  
…  
```
 
这样的代码就存在前面提到的问题，如果修改常量值就需要重新编译引用常量的类。
但我们可以换一种方式，
在HttpUtil类中增加一个重载方法：
 
Java代码  
```java
public static void saveFile(HttpServletRequest req) {  
  saveFile(req, MAX_SIZE);  
}  
```
 
这样在别的模块需要上传附件并且仅使用默认文件大小限制的时候，就可以调用这个方法。
好处就是现在你再修改MAX_SIZE的值，只需要编译这一个类就可以了。
