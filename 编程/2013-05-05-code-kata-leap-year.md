Date: 2013-05-05 11:32
Title: code kata - leap year（闰年）
Url: /code-kata-leap-year
Tags: code, tdd, IntelliJ IDEA

#### 活动感想
虽然接触TDD已有一段时间，但实践的并不多，所以理解也很浅。在此次活动中，很多同学是第一次接触TDD，也犯了普遍会犯的错误，比如先把所有测试代码全部写完才开始写实现代码，大粒度的测试和忘记重构等。网上也有很多关于TDD的争论，比如：
[TDD并不是看上去的那么美](http://coolshell.cn/articles/3649.html)
[敏捷质疑：TDD](http://www.blogjava.net/chelsea/archive/2008/07/13/214623.html)
如果没有TDD经验，很难看懂他们在说什么，也没有办法参与讨论。所以我们还是先实践吧。

#### 需求
>Write a function that returns true or false depending on
whether its input integer is a leap year or not.
>A leap year is defined as one that is divisible by 4,
but is not otherwise divisible by 100 unless it is
also divisible by 400.
>For example, 2001 is a typical common year and 1996
is a typical leap year, whereas 1900 is an atypical
common year and 2000 is an atypical leap year.

#### 第一版视频
<embed src="http://www.tudou.com/v/kzZa8L2r1Vg/&resourceId=0_05_05_99&bid=05/v.swf" type="application/x-shockwave-flash" allowscriptaccess="always" allowfullscreen="true" wmode="opaque" width="480" height="400"></embed>

#### 反馈
视频发出后收到了很多的反馈，下面挑一些比较典型的，感谢大家给予的关注的和反馈：
[Daniel Teng](http://www.danielteng.com/)：

	多谢Seaborn分享。我来抛一下砖

	+ 第一次分享
	+ 用IntelliJ
	+ 从失败测试开始
	+ 有一些算法重构
	+ 频繁运行测试
	+ 左右和上下分屏，包括把测试放在左边，代码放在右边

	- 在视频中加一些文本解释
	- 来电激情的背景音乐，最好是钢琴的。
	- 用了太多的鼠标和菜单，IntelliJ不停地在提示快捷键ctrl+shift+R已经提醒了22遍
	- 使用在IDE中切换、跳转、选择等的快捷键
	- 测试函数的名称。我的习惯是，测试函数的名字沟通的是测试的目的，应该代表抽象概念，然后测试体内是使用具体的例子。举一个例子year_2000_is_an_atypical_leap_year。我可能会直接写atypical_leap_year，因为我不想把例子和抽象概念放在一起。想一下，下一个维护测试的人，看到你的case会想，“这个地方只是测了2000年是一个特殊的闰年，那我还要不要看一下3000。。。”
	- 把year从测试函数名字中去掉，因为你的测试类名已经在沟通这个概念了
	- 不太喜欢calculator这个名字，这不像是一个domain概念。
	- 有些步骤跳跃比较大，有一步直接加了if (i % 4 == 0)，对我来说相当于同时添加了一个条件判断和一个表达式，步骤有可能比较大。如果kata的话，应该练习小步，或者从if+hard code开始？（当然在这个例子上，我也没有想好好的理由，只不过个人习惯）。
	- 修改模板，去掉默认注释，因为那是没用的，source control都会记录这些信息，既然已经有了，而且别人又不会去维护，那为什么不马上去掉？

[Larry Cai](http://weibo.com/124565421)：

	还不错，有些跳得太快我来不及看，有些有些冗余。
	不要简单记录你做的，调整一下节奏，试着边讲边练就可以把重要的东西慢下来。
	用快捷键，省得满屏乱窜。

	这是个技术活,我也不得要领。

[姚若舟](http://weibo.com/yaoruozhou)
    
    Joseph将我的视频加入到了[kata接力][5]活动中，我们针对一个方法命名进行了一些讨论。    
![讨论截图](/_image/2013-07-25/20-51-22.jpg)
[5]: http://qing.blog.sina.com.cn/1887029092/7079c76433002zol.html?rnd=0.4563624414149672

#### 关于视频制作
第一次在Mac上录制屏幕，使用的是QuickTime Player，会有一点点卡，影响不大。
另外，视频后期编辑使用了iMovie。
视频一开始上传到youku上，发现不是很清楚，然后传到tudou就清楚多了。

#### 关于BJDP
>BJDP全称是（Beijing Design Patterns Study Group），即北京设计模式学习组，专注北京线下结对编程操练。让你通过面对面的分享讨论和肩并肩的结对编程(Coding Dojos编程道场)来领悟编程匠艺(Software Craftsmanship)之道。

[BJDP第一次线下活动报道](http://www.ituring.com.cn/article/39938)

网站：[www.bjdp.org](http://www.bjdp.org/)
QQ群：235913915
